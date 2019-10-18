```python
# SQLAlchemy 使用

from sqlalchemy import create_engine
engine = create_engine('sqlite:///test', echo=True)
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()
from sqlalchemy import Column, Interval, String)

class User(Base):
    __tablename__ = 'users'
    user_id = Column(Integer, primary_key=True)
    name = Column(String(50))

Base.metadata.create_all(engine)

from sqlalchemy.orm import sessionmaker

Session = sessionmaker()
session = Session()

session.add()
session.delete()
session.query()
session.commit()
session.rollback()
```

```python
# Flask_SQLAlchemy
from flask_sqlalchemy import SQLAlchemy
db = SQLAlchemy()

class User(Base):
    __tablename__ = 'users'
    user_id = Column(Integer, primary_key=True)
    name = Column(String(50))

def create_app():
	app = Flask(__name__)
	db.app = app
	db.init_app(app)
```

