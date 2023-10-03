# How To : pandas_geopandas

## Méthode pour dataframe et geodataframe

### Définir le typage des colonnes en lisant un fichier csv

```python
df = pd.read_csv("path/to/file/.csv", low_memory=False,dtype={'col1':'str','col2':'type2'},nrows=1000)
```

### Définir le typage des colonnes pendant un bulk

```python
df.to_sql("table_name", db ,if_exists='append', dtype={'col1':'str','col2':'type2'})
gdf.to_postgis('table_name', db, if_exists='append', index=False, schema="test", dtype={'geometry': Geometry('POINT', srid='4326')})
```

### Augmenter le nombre de colonnes afficher avec le print

```python=
pd.set_option('display.max_columns', 50) 
```

### Renommer une ou plusieurs colonne

```python=
df = df.rename(columns={"old_name_col1":"new_name_col1","old_name_col2":"new_name_col2"})
```

```python=
df = df.rename({"old_name_col1":"new_name_col1","old_name_col2":"new_name_col2"},axis=1)
```

### Changer le type d'une colonne

```python=
df = df.astype({'col_name': 'new_type'}) #ex : str
```

### Supprimer des colonnes via leur nom

```python=
df = df.drop(columns=["col1","col2"],axis=1)
```

### Supprimer les n premièreres colones

```python=
df = df.tail(-n)
```

### Récupérer l'index d'une colonne via son nom

```python=
df.columns.get_loc("pear")
```

### Réindexer et supprimer l'acienne colonne d'index

```python=
df = df.reset_index(drop=True)
```

### Utiliser le slicing et l’enumération d’index dans un iloc

```python=
import numpy as np

gdf = gdf.drop(gdf.iloc[:,np.r_[11:16,31]],axis=1)
```

⚠️Cela ne marche pas s'il n'y a pas un index de chaque côté du :

```python=
❌ : gdf = gdf.drop(gdf.iloc[:,np.r_[0,11:]],axis=1)
✅ : gdf = gdf.drop(gdf.iloc[:,np.r_[0,11:len(gdf.columns)]],axis=1)
```

### Bonnes pratiques pour séléctionner des données d'un dataframe

```python=
❌ : df["col"][index]
✅ : df.loc[index,col]
```

La première méthode est mauvaise car elle effectue la séléction en 2 étapes(séléction de la colonne, puis accès au bon index). A l'inverse la seconde formule est effectuée en un seul bloc et est donc plus rapide.

ressources : <https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy>

### Appliquer une fonction sur une ou plusieurs colonnes d'un dataframe

#### Une colonne

```python=
 df["lat"] = df["lat"].apply(lambda x: x.replace(",","."))

```

#### Avec un else

```python=
df["code_5digits"] = df["code"].apply(lambda x: x[0:5] if len(x) == 9 else "0" + x[0:4])
```

#### Plusieurs colonnes

```python=
 df[["lat","long"]] = df[["lat","long"]].apply(lambda x: x.str.replace(",","."))
```

#### Fonction custom

```python=
def my_function(x):
    return x == 0

[...]

df['col'] = df['2021'].apply(lambda x: my_function(x))
```

#### String replace de plusieurs patterns vers une seule string retour

```python=
targets = {"BMRA |SPAFT |CSP |CIAT  "}
replace_with = [""]
df.col = df.col.replace(targets,replace_with,regex=True)
```

### Faire un ou dans une séléction de valeurs

```python=
df = df[(df['col'].str.contains('string1')) | (df['col'].str.contains('string2'))]
```

### Créer un dataframe à partir d'un autre en récupérant des valeurs clés

```python=
df = pd.read_csv("path/to/file")
update = []
update.append(gdf.loc[gdf["col"] == value].values.flatten().tolist())
df2 = pd.DataFrame(update,columns=df.columns)
```

### Créer un geodataframe à partir d'un dataframe avec des coordonnées

```python=
gdf = geopandas.GeoDataFrame(
    df, geometry=geopandas.points_from_xy(df.Longitude, df.Latitude))
```

```python=
gdf = geopandas.GeoDataFrame(
    df, geometry=geopandas.points_from_xy(df["Longitude"], df["Latitude"]))
```

### Créer un dataframe ou un geodataframe à partir d’un autre, en créant un dictionnaire

```python=
for row in df.itertuples(index=False):
  d = {"dep":[row[0]],"niveau":[row[1]],"alea":[row[2]],"geometry":row[3]}
  df_tmp = pd.DataFrame(d)
  df_tmp = gpd.GeoDataFrame(df_tmp,geometry=df_tmp.geometry,crs="EPSG:4326")
```

### Créer un dataframe ou un geodataframe à partir d’un autre, sans dictionnaire

```python=
for row in df.index:
    df_tmp = df.iloc[[row],:]
```

### Combiner 2 dataframes et ne garder que la colonne qui n'est pas nulle

```python=
res = pd.merge(df_1, df_2, on="ref_col", how="left")
res["col1"] = np.NAN
for i, row in res.iterrows():
    val = row["col1_x"]
    if pd.isnull(val):
        val = row["col1_y"]
    res.at[i, 'col1'] = val
df = res.drop(["col1_x", "col1_y"],axis=1)
```

Avec un terner

```python=
res = pd.merge(df_1, df_2, on="ref_col", how="left")
res["col1"] = np.NAN
for i, row in res.iterrows():
    val = row["col2_x"] if pd.isnull(row["col1_x"]) else row["col1_x"]
    res.at[i, 'col1'] = val
df = res.drop(["col1_x", "col1_y"],axis=1)
```

### Bulk dans postgres une grosse table par morceaux

```python=
df.to_sql('table_name',engine,if_exists='fail',index=False,chunksize=10000)
```

## Méthode pour dataframe

### Extraire des dataframes d'un excel avec plusieurs feuilles

```python=
excel = pd.ExcelFile(self.save_path + "/criminality.xlsx")
sheets = excel.sheet_names
for elm in sheets:
    df =  pd.read_excel(excel, elm)
```

### Récupérer un geodataframe venant de postgis sous forme d'un dataframe

```python=
query = f"""
    SELECT dpt, niveau, alea, ST_AsText(ST_Transform(geometry, 4326)) as           geometry
    FROM source_table
    """

df = pd.read_sql(query, my_db)
    df['geometry'] = df['geometry'].apply(wkt.loads)
    df = gpd.GeoDataFrame(df,geometry=df['geometry'], crs="EPSG:4326")
```

⚠️ Avec cette méthode certaines géometries peuvent se corrompre après la récupération.

### Pivoter un dataframe avec information sur les lignes en dataframe avec information sur les colonnes

```python=
df = df.pivot(index=["col_index"],columns=["new_agglomerate_col1","new_agglomerate_col1"],values=["old_val1","old_val2"]).reset_index() #pivot the table 
#concatenate the columns name from tuples one string per column
new_cols=[('{0}_{2}_{1}'.format(*tup)).replace(" ","_") for tup in df.columns]
df.columns = new_cols

```

### Exemple

Dataframe avant pivot :

```plaintext=
   taux/mille  insee  annee  classe  \
0    2.197535  01001     21  type1   
1    1.920608  01001     20  type1   
2    2.066543  01001     19  type1   
3    2.356525  01001     18  type1   
4    2.291806  01001     17  type1   

    nb_fait  taux_pour_mille  
0  2.087413         2.197535  
1  1.816609         1.920608  
2  1.893103         2.066543  
3  2.173469         2.356525  
4  2.098639         2.291806 
```

Après pivotage :

```plaintext=
   insee  nb_fait_type1_21  \
0  01001                                           2.087413    
1  01002                                           2.087413    
2  01004                                         140.000000    
3  01005                                           2.087413    
4  01006                                           0.000000    

   nb_fait_type1_20  \
0                                           1.816609    
1                                           1.816609    
2                                          98.000000    
3                                           1.816609    
4                                           0.000000    

   nb_fait_type1_19  ...  \
0                                           1.893103   ...   
1                                           1.893103   ...   
2                                         116.000000   ...   
3                                           1.893103   ...   
4                                           0.000000   ...   

   taux/mille_type2_17  \
0                                           0.000000             
1                                           0.000000             
2                                           2.636266             
3                                           0.905221             
4                                           0.905221             

   taux/mille_type2_20  \
0                                           1.148000             
1                                           0.000000             
2                                           2.193293             
3                                           0.000000             
4                                           1.148000             

   taux/mille_type2_18  \
0                                           0.000000             
1                                           0.000000             
2                                           2.393692             
3                                           0.994701             
4                                           0.994701             

   taux/mille_type2_19  
0                                           1.149877            
1                                           0.000000            
2                                           3.113061            
3                                           1.149877            
4                                           1.149877
```

### Transformer un geodataframe en dataframe

```python=
gdf["geometry"] = gdf["geometry"].apply(lambda x: str(x))
```

Permet de stocker des données géospatiales dans une base postgres standard.

## Méthode pour geodataframe

### Charger un geopackage avec geopandas (.shp .cpg .dbf .prj .qix .shp .shx)

Marche aussi avec les fichiers json et geojson.
Peut lire des dossiers compréssés

```python=
return gpd.read_file("path_to_folder_with_files")
```

### Initialiser / Transformer le crs d'un geodataframe

```python=
gdf = gdf.set_crs(4326) #4326 = WGS84
```

```python=
gdf = gdf.to_crs(4326)
```

### Vérifier la validité des géométries d'un geodataframe, le cas échéant les corrigées

```python=
from shapely.validation import explain_validity

for i in df.index:
    if explain_validity(df.geometry[i]) != "Valid Geometry":
        #correction for invalid geometry
        df.geometry[i] = df.geometry[i].buffer(0)
```

### Supprimer un polygone contenu dans un multipolygon d’un geodataframe

```python=
pol = gdf.iloc[0][0] #récupère le polygone dans le gdf
pols = list(pol.geoms) #stocke les polygones dans une liste
pols = pols[1:] #supprimer le premier poly de la liste
multi = MultiPolygon(pols) #recréer un mutlipolygone à partir de la liste
df = pd.DataFrame({'geometry':multi}) #créer un df à partir du multipoly
new_df = gpd.GeoDataFrame(geometry=df['geometry'],crs="EPSG:4326") #créer un gdf à partir du df
```

### Fusionner les géométries d'un geodataframe avec une colonne de référence, puis faire l'intersection avec un autre geodataframe

La fusion des polygones est facultative mais peut être nécessaire pour des soucis d'optimisation.

```python=
#création df du résultat
result = pd.DataFrame(columns= ["insee_com","dep","niveau","geometry"])
for elm in df["niveau"].unique():
    # sélectionne les lignes qui ont une des valeurs de la colonne de référence
    df_tmp = df.loc[df["niveau"] == elm]

    #fusionne les polygones en un seul
    new_pol_geom = df_tmp["geom"].unary_union

    #recréation d'un géodataframe
    df_one_poly = pd.DataFrame(
    {
        'dep':[dep],'niveau':[elm], 'geometry':[new_pol_geom]
    })
    df_one_poly = gpd.GeoDataFrame(df_one_poly ,geometry=df_one_poly.geometry,crs="EPSG:4326")
    
    #intersection
    inter_carr = gpd.overlay(df_2_tmp, df_one_poly, how='intersection',keep_geom_type=False)

    # ajoute au résultat si non vide
    if inter_carr.empty == False:
        result = pd.concat([result, inter_carr], ignore_index=True)

    result = gpd.GeoDataFrame(result,geometry=result.geometry,crs="EPSG:4326")
```

### Séléctionner les points d'un geodataframe qui sont dans un polygone

⚠️ La représentation cartographique(crs) doit être la même entre les 2 geodataframe

```python=
from geopandas.tools import sjoin

pointsInpolys = sjoin(gdf_point,gdf_pol,how="left")
pointsInpolys = pointsInpolys[pointsInpolys["index_right"].notna()]
pointsInpolys = pointsInpolys.reset_index()
pointsInpolys = pointsInpolys.drop(columns=["index_right","index"],axis=1)
```

### Trouver le point le plus proche dans un geodataframe et sa distance

```python=
from shapely.ops import nearest_points

destinations = MultiPoint(gdf['geometry'])
nearest_point = nearest_points(orig,destinations)[1] #origine est le point dont on veut le plus proche voisin dans le geodataframe
print(df.loc[df["geometry"] == nearest_point]) #affiche l'index du point le plus proche dans le geodataframe
    
#récupérer la distance
df_p = gpd.GeoDataFrame({'geometry' : [orig, nearest_point]}, crs='EPSG:4326')
df_p.to_crs(epsg=3310,inplace=True) #la réprsentation 331à est plus adaptée pour le calcul de distance
s = df_p.distance(df_p.shift())
distance = s.iloc[1]
```

### Contourner l'erreur suivante lors d'un bulk postgis en if_exists = "append"

```python=
psycopg2.errors.InvalidParameterValue: ERREUR:  Geometry type (MultiPolygon) does not match column type (Polygon)

CONTEXT:  COPY g_pol_perimetre, ligne 11, colonne geometry : \xc2\xab *
```

```python=
from shapely.geometry import Polygon,MultiPolygon

gdf["geometry"] = [MultiPolygon([feature]) if isinstance(feature, Polygon) \
                    else feature for feature in gdf["geometry"]]
```
