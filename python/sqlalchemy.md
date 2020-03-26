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
class BaseModel(AbstractConcreteBase, db.Model, JSONBaseMixin):
    __abstract__ = True

    create_time = db.Column(db.DateTime, server_default=func.now(), default=datetime.now, index=True)
    timestamp = db.Column(db.Integer, default=time.time, index=True)
    is_valid = db.Column(db.Boolean, default=True, index=True)
    update_time = db.Column(db.DateTime, server_default=func.now(), default=datetime.now, onupdate=datetime.now,
                            index=True)

    @declared_attr
    def __tablename__(cls):
        return cls.__name__.lower()


class BaseModelIntPK(BaseModel):
    __abstract__ = True
    object_id = db.Column(db.Integer, primary_key=True, nullable=False, autoincrement=True)


class BaseModelUUIDPK(BaseModel):
    __abstract__ = True
    object_id = db.Column(db.String(32), default=gen_id, primary_key=True)

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

### relationship

- 一对多

```python
# 一个A对多个B
class A(Model):
    __tablename__ = "a"
    id = Colume(Integer, primary_key=True)
    # 1单向
    bs = relationship("B")
    # 2双向
    bs = relationship("B", back_populates="a")
    # 3双向
    bs = relationship("B", backref="a")
# 写在类外面
A.bs = relationship("B", backref="a")
class B(Model):
    __tablename__ = "b"
    id = Column(Integer, primary_key=True)
    a_id = Column(Integer, ForeignKey("a.id"))
    # 2双向
    a = relationship("A", back_populates="bs")
```



- 多对一

```python
# 多个A对一个B
class A(Model):
    __tablename__ = "a"
    id = Colume(Integer, primary_key=True)
    b_id = Column(Integer, ForeignKey("b.id"))
    # 1单向
    b = relationship("B")
    # 2双向
    b = relationship("B", back_populates="a")
    # 3双向
    b = relationship("B", backref="as", foreign_keys=[b_id])
    foreign_keys="[b_id]"/"b_id"
# 写在类外面
A.b = relationship("B", backref="as")
class B(Model):
    __tablename__ = "b"
    id = Column(Integer, primary_key=True)
    # 2双向
    as = relationship("A", back_populates="b")
```



- 一对一

```python
# 一对一是一对多或多对一的特殊情况，在哪个表中加外键哪个表就是一，关系加上uselist参数
# 一个A对一个B
class A(Model):
    __tablename__ = "a"
    id = Colume(Integer, primary_key=True)
    # 2双向
    b = relationship("B", back_populates="a", uselist=False)
    # 3双向
    b = relationship("B", backref=backref("a", uselist=False))
class B(Model):
    __tablename__ = "b"
    id = Column(Integer, primary_key=True)
    a_id = Column(Integer, ForeignKey("a.id"))
    # 2双向
    a = relationship("A", back_populates="b")
```



- 多对多

```python
# 多对多需要在两个表中间增加中间表实现，relationship通过增加secondary参数实现
class A(Model):
    __tablename__ = "a"
    id = Colume(Integer, primary_key=True)
    # 2使用populates
    bs = relationship("B", secondary=c, back_populates="as")
    # 3使用backref
    bs = relationship("B", secondary=c, backref="as")
    
class B(Model):
    __tablename__ = "b"
    id = Column(Integer, primary_key=True)
    # 2使用populates
	as = relationship("A", secondary=c, back_populates="bs")
    
c = Table(
    'c', 
    Column("a_id", Integer, ForeignKey("a.id")),
    Column("b_id", Integer, ForeignKey("b.id")),
)
# 删除(自动处理中间表) (级联, on delete cascade)
qurey_a.bs.remove(someb)


### 关联对象 多对多关系
“”“
使用class声明新表代替Table()
```

- 邻接表关系

```python
# 简单邻接表，表中有一个外键指向自己
class Node(Model):
    __tablename__ "node"
    id = Column(Integer, primary_key=True)
    parent_id = Column(Integer, ForeignKey("node.id"))
    # 单向，默认一对多关系
    children = relationship("Node")
    # 反向，通过remote_side增加多对一关系
    parent = relationship("Node", remote_side=[id])
    # 双向 使用backref
    children = relationship("Node", backref=backref("parent", remote_side=[id]), lazy="joined", join_dep=2)
```





邻接表（父id），路径枚举（存路径），嵌套集（左右值），闭包表（节点表+关系表）

```python
lazy参数
默认 返回查询结果
dynamic 返回查询，后边能接filter，多次动态查询，
joined 返回查询结果，使用联结
subquery 返回查询结果，使用subquery
select 返回查询结果

https://www.jianshu.com/p/8427da16729a
```



```python
class AbsCheckApply(Model):
	__abstract__ = True

# 分表
class CheckApplyShard(object):
    _mapper = {}

    @staticmethod
    def model(enterprise_id):
        if len(enterprise_id) <= 1:
            table_index = "00"
        else:
            if enterprise_id == "-1":
                table_index = "00"
            else:
                table_index = enterprise_id[-2:]
        class_name = "ZCheckApply_%s" % table_index

        ModelClass = CheckApplyShard._mapper.get(class_name, None)
        if ModelClass is None:
            ModelClass = type(class_name, (AbsCheckApply, JSONBaseMixin,), {
            })
            CheckApplyShard._mapper[class_name] = ModelClass

        return ModelClass

```

