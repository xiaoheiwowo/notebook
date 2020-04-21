- WSGI: web服务器和python程序的接口约定

## WSGI 服务器
一个 WSGI 服务器需要实现两个函数：

1. 解析 http 请求，为应用程序提供 environ 字典
2. 实现 start_response 函数 


## python程序

实现一个接受environ和start_response的函数, 在函数内部要调用start_response

- 通过 __call__ 方法将 Flask 对象变为可调用
- 实现 wsgi_app 函数处理 web 服务器转发的请求 wsgi_app(self, environ, start_response)

## 路由

```python
app = Flask("app")

@app.route("/")
def index():
	return "index"

```

- url_map: 关联url和endpoint 
	- Map([<Rule '/' (HEAD, OPTIONS, GET) -> index>,])
- view_functions: 关联endpoint和视图函数
	- {'index': <function index at 0x10eaf9e60>}
		
- 添加url的几种方法
```python
@app.route()
app.add_url_rule("/hello", hello)
app.add_url_rule("/class_view", Class.as_view("class"))

```

- 通过 Map 我们就可以实现动态路由的功能。这里我们注意到 Map 类先建立了 url 到 endpoint 的映射，而不是直接映射到函数，这是为什么呢？
- 主要是两个原因：
	- 一是为了实现动态路由功能，
	- 二是为不同的 url 映射到同一个视图函数提供了便利。
- 而 view_functions 是一个字典，它负责建立 endpoint 和视图函数之间的映射关系。eg: url_for(endpoint)


## 请求分发 dispatch_request