# How to : SQL query

## Récupérer le chemin du fichier de configuration pg_hba.conf pour PostgreSQL

```postgresql
SHOW hba_file;
```

### Faire une requête avec un distinct surune colonne mais pas sur les autres

```postgresql
SELECT distinct_col,other_col1
    FROM (
        SELECT distinct_col,other_col1,
        ROW_NUMBER() OVER(PARTITION BY distinct_col) rn
        FROM table
        ) a 
    where rn = 1 
```

### Ajouter un 0 à une string dans une colonne pour avoir le bon nombre de charactères

```postgresql
UPDATE table
SET col = concat('0', col)
WHERE char_length(col) < len_wanted --ex:2
```

### Supprimer les doublons de lignes

```postgresql
DELETE FROM table
WHERE ctid NOT IN
    (
        SELECT MAX(ctid)
        FROM table
        GROUP BY
        schema.table.*
    );
```

### Faire des comptages basés sur la valeur d'une colonne groupé sur la valeur d'une autre colonne

```postgresql
select col1,
sum(case when "col1" = 'val' then 1 else 0 end) as count_col1
FROM table
group by col1
order by col1
```

### Compter les valeurs identiques d'une colonne et les regroupées par valeurs

```postgresql
SELECT col, COUNT(*) AS count_col
FROM table
GROUP BY col
```

### Supprimer toutes les tables dans un schema

```postgresql
SELECT concat('DROP TABLE "',  TABLE_NAME , '"') 
FROM INFORMATION_SCHEMA.TABLES 
where table_schema in ('my_schema') and TABLE_NAME LIKE 'myregex%'
```

Récupérer le résultat de la query, l'éditer avec vscode pour ajouter des ; puis exécuter le résultat.
