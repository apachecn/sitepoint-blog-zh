# Python Web 应用程序:WSGI 的基础

> 原文：<https://www.sitepoint.com/python-web-applications-the-basics-of-wsgi/>

![The basics of WSGI](img/fa72860ac4ea93ffc1116220213ef388.png)

在 [Django](https://www.djangoproject.com/) 、 [Flask](http://flask.pocoo.org/) 、 [Bottle](http://bottlepy.org/docs/dev/index.html) 和其他所有 Python web 框架之下，是 web 服务器网关接口，简称 WSGI。WSGI 对于 Python 就像 Servlets 对于 Java 一样——web 服务器的通用规范，允许不同的 web 服务器和应用程序框架基于通用 API 进行交互。然而，和大多数事情一样，Python 版本要简单得多。

WSGI 是在 [PEP 3333](https://www.python.org/dev/peps/pep-3333/) 中定义的，如果您想在这个快速介绍后了解更多信息，我鼓励您阅读它作为参考。

本文将从应用程序开发人员的角度向您介绍 WSGI 规范，并向您展示如何直接使用 WSGI 来创建应用程序(如果您愿意的话)。

## 你的第一个 WSGI 应用

下面是最基本的 Python web 应用程序:

```
def app(environ, start_fn):
    start_fn('200 OK', [('Content-Type', 'text/plain')])
    return ["Hello World!\n"] 
```

就是这样！整个文件。将其命名为 app.py，并在任何兼容 WSGI 的服务器上运行它，您将得到一个 Hello World 响应，状态为 200。为此，您可以使用 gunicorn 只需通过 pip ( `pip install gunicorn`)安装并用`gunicorn app:app`运行即可。这个命令告诉 gunicorn 从 app 模块中的 app 变量获取 WSGI callable。

现在，你应该很兴奋。一个正在运行的应用程序只需要 3 行代码？那一定是某种记录(排除 PHP，因为 mod_php 是作弊)。我打赌你只是渴望知道更多。

那么 WSGI 应用程序的基本部分是什么呢？

*   WSGI 应用程序是一个 Python *可调用的*，比如一个函数、一个类或者一个带有`__call__`方法的类实例
*   应用程序 callable 必须接受两个参数:一个是包含请求数据的 Python 字典`environ`，另一个是 callable 本身`start_fn`。
*   应用程序必须用两个参数调用`start_fn`:状态代码(作为一个字符串),以及一个表示为 2 元组的头列表。
*   应用程序返回一个 iterable，其中包含响应体中的字节，这些字节是方便的、可流式传输的数据块——在本例中，是一个只包含`"Hello, World!"`的字符串列表。(如果`app`是一个类，这可以在`__iter__`方法中完成。)

举例来说，接下来的两个示例与第一个示例相同:

```
class app(object):

    def __init__(self, environ, start_fn):
        self.environ = environ
        self.start_fn = start_fn

    def __iter__(self):
        self.start_fn('200 OK', [('Content-Type', 'text/plain')])
        yield "Hello World!\n" 
```

```
class Application(object):
    def __call__(self, environ, start_fn):
        start_fn('200 OK', [('Content-Type', 'text/plain')])
        yield "Hello World!\n"

app = Application() 
```

您可能已经在考虑使用这些信息的方法，但最相关的可能是编写中间件。

## 振作起来

中间件是扩展 WSGI 应用程序功能的一种简单方法。因为您只需要提供一个 callable，所以您可以随意将它包装在其他函数中。

例如，假设我们要检查`environ`的内容。我们可以很容易地创建一个中间件来做到这一点，如下例所示:

```
import pprint

def handler(environ, start_fn):
    start_fn('200 OK', [('Content-Type', 'text/plain')])
    return ["Hello World!\n"]

def log_environ(handler):
    def _inner(environ, start_fn):
        pprint.pprint(environ)
        return handler(environ, start_fn)
    return _inner

app = log_environ(handler) 
```

这里，`log_environ`是一个返回函数的函数，该函数在遵从最初的回调之前对`environ`参数进行了美化。

以这种方式编写中间件的好处是中间件和处理程序不必知道或关心对方。例如，你可以很容易地将`log_environ`绑定到一个 [Flask](http://flask.pocoo.org/) 应用上，因为 Flask 应用是 WSGI 应用。

一些其他有用的中间件想法:

```
import pprint

def handle_error(handler):
    def _inner(environ, start_fn):
        try:
            return handler(environ, start_fn)
        except Exception as e:
            print e  # Log error
            start_fn('500 Server Error', [('Content-Type', 'text/plain')])
            return ['500 Server Error']
    return _inner

def wrap_query_params(handler):
    def _inner(environ, start_fn):
        qs = environ.get('QUERY_STRING')
        environ['QUERY_PARAMS'] = urlparse.parse_qs(qs)
        return handler(environ, start_fn)
    return _inner 
```

如果你不想在文件的底部形成一个大金字塔，你可以使用`reduce`一次应用一堆中间件:

```
# Applied from bottom to top on the way in, then top to bottom on the way out
MIDDLEWARES = [wrap_query_params,
               log_environ,
               handle_error]

app = reduce(lambda h, m: m(h), MIDDLEWARES, handler) 
```

通过利用`start_fn`参数，您还可以编写修改响应的中间件。这里有一个中间件，如果`Content-Type`头是`text/plain`，它将反转输出:

```
def reverser(handler):

    # A reverse function
    rev = lambda it: it[::-1]

    def _inner(environ, start_fn):
        do_reverse = []  # Must be a reference type such as a list

        # Override start_fn to check the content type and set a flag
        def start_reverser(status, headers):
            for name, value in headers:
                if (name.lower() == 'content-type'
                        and value.lower() == 'text/plain'):
                    do_reverse.append(True)
                    break

            # Remember to call `start_fn`
            start_fn(status, headers)

        response = handler(environ, start_reverser)

        try:
            if do_reverse:
                return list(rev(map(rev, response)))

            return response
        finally:
            if hasattr(response, 'close'):
                response.close()
    return _inner 
```

由于`start_fn`和 response 的分离，这有点复杂，但仍然完全可行。

还要注意，为了严格遵循 WSGI 的规范，我们必须检查响应中的 <q>close</q> 方法，如果存在就调用它。遗留的 WSGI 应用程序[也可能在调用`handler`时返回一个<q>写</q>函数](https://www.python.org/dev/peps/pep-0333/#the-write-callable)，而不是一个 iterable 如果您希望您的中间件支持旧的应用程序，您可能需要处理这种情况。

一旦你开始使用原始的 WSGI，你就会开始理解为什么 Python 有几十个 web 框架。WSGI 使得从零开始构建东西变得非常简单。例如，您可能正在考虑路由问题:

```
routes = {
    '/': home_handler,
    '/about': about_handler,
}

class Application(object):
    def __init__(self, routes):
        self.routes = routes

    def not_found(self, environ, start_fn):
        start_fn('404 Not Found', [('Content-Type', 'text/plain')])
        return ['404 Not Found']

    def __call__(self, environ, start_fn):
        handler = self.routes.get(environ.get('PATH_INFO')) or self.not_found
        return handler(environ, start_fn) 
```

如果您喜欢灵活地组装库，那么直接使用 WSGI 会很好

*   模板库:只需放入任何你喜欢的模板库(例如 [Jinja2](http://jinja.pocoo.org/) ， [Pystashe](https://github.com/defunkt/pystache) )并从你的处理程序返回渲染模板！
*   用像 [Routes](http://routes.readthedocs.io/en/latest/) 或者 [Werkzeug 的 routing](http://werkzeug.pocoo.org/docs/0.11/tutorial/#step-4-the-routing) 这样的库来帮助你的路由。实际上，如果您想在 WSGI 上使用一个非常细微的抽象，可以看看 Werkzeug。
*   像使用 Flask 或类似工具一样使用任何数据库/迁移库。

当然，对于非专业的应用程序，您可能仍然希望使用一个框架，以便恰当地处理边缘情况等等。

## 但是服务器呢？

有很多方法可以提供 WSGI 应用程序。我们已经讨论过 [Gunicorn](http://gunicorn.org/) ，这是一个不错的选择。 [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/) 是另一个很好的选择。只要确保您在这些之前设置了类似 nginx 的东西来服务静态资产，您就应该有一个坚实的起点。

这就是全部了！

## 分享这篇文章