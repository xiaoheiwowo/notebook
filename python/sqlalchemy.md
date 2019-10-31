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

```python
# sqlalchemy.sql.expression.case
def get_enterprise_member_count(enterprise_ids):
    # CASE usr.enterprise_id WHEN :param_1 THEN :param_2 WHEN :param_3 THEN :param_4 END
    ordering = sqlalchemy.sql.expression.case(
        {id: index for index, id in enumerate(enterprise_ids)},
        value=Usr.enterprise_id
    )
    # WHERE usr.enterprise_id IN (%s, %s) GROUP BY 
    # usr.enterprise_id ORDER BY CASE usr.enterprise_id WHEN %s THEN %s WHEN %s THEN %s END
    querys = db.session.query(
        Usr.enterprise_id, 
        db.func.count(Usr.object_id)
    ).filter(
        Usr.enterprise_id.in_(enterprise_ids), 
        Usr.state==EmployeeState.normal.value
    ).group_by(Usr.enterprise_id).order_by(ordering).all()
    
    res = []
    for query in querys:
        res.append({'enterprise_id': query[0], 'member_count': query[1]})
    return succeed('', res)

# 示例2
case(value=emp.c.type, 
     whens={
        'engineer': emp.c.salary * 1.1,
        'manager':  emp.c.salary * 3,
     })
```

