```python
class OperationLog(Document):
    platform = Integer()
    create_time = Date()
    enterprise_id = Text()
    employee_id = Text()
    modular = Integer()
    title = Text()
    content = Text()

    class Index:
        name = 'operation'
        settings = {
          "number_of_shards": 2,
        }

    def save(self, ** kwargs):
        return super(OperationLog, self).save(** kwargs)
    
    
   
@api.route('/operation/create', methods=['POST'])
@check_request_params(
    enterprise_id=("enterprise_id", True, CheckType.other),
    employee_id=("employee_id", True, CheckType.other),
    title=("title", True, CheckType.other),
    content=("content", True, CheckType.other),
    platform=("platform", True, CheckType.int),
    modular=("modular", True, CheckType.int),
)
def create(enterprise_id, employee_id, title, content, platform, modular):
    operationLog = OperationLog()
    operationLog.enterprise_id = enterprise_id
    operationLog.employee_id = employee_id
    operationLog.title = title
    operationLog.content = content
    operationLog.platform = platform
    operationLog.modular = modular
    operationLog.create_time = datetime.now()
    operationLog.save()
    return succeed()


@api.route('/operation/get', methods=['GET'])
@check_request_params(
    enterprise_id=("enterprise_id", True, CheckType.other),
    start_date=("start_date", True, CheckType.date),
    end_date=("end_date", True, CheckType.date),
    modulars=("modulars", True, CheckType.json),
    employee_id=("employee_id", False, CheckType.other),
    page_size=("page_size", True, CheckType.int),
    page_num=("page_num", True, CheckType.int),
)
def get(enterprise_id, start_date, end_date, modulars, employee_id, page_size, page_num):
    logs = OperationLog.search().query("match_phrase", enterprise_id=enterprise_id)\
        .query("range", **{'create_time': {'gte': start_date, "lte": end_date}})\
        .query("terms", modular=modulars)\
        .sort("-create_time")
    if employee_id:
        logs = logs.query("match_phrase", employee_id=employee_id)

    total = logs.count()
    page_index = page_num - 1
    logs = logs[page_size*page_index: page_size*page_index + page_size]
    res = []
    for log in logs.execute():
        l = log.to_dict()
        l["create_time"] = l["create_time"].strftime('%Y-%m-%d %H:%M:%S')
        res.append(l)
    return succeed('', {"total":total,"data":res})

```

