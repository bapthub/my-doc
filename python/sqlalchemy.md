# How To : sqlalchemy

## Créer une table vide via une classe et la bulk dans une base

```python
from geoalchemy2 import Geometry
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String, Float, Boolean,create_engine

Base = declarative_base()

class Table(Base):
   __tablename__  = 'nom_de_table'
   __table_args__ = {'schema': 'nom_de_schema'}
   
   col1 = Column(String(14), primary_key=True,)
   col2 = Column(Integer)
   col3 = Column(Float)
   col4 = Column(Boolean)
   col5 = Column(String)
   geometry = Column(Geometry(geometry_type="POINT", srid='4326'))

engine = create_engine('postgresql://'+user+":"+password+'@'+host+":"+port+'/'+base)
Base.metadata.create_all(engine)
```
