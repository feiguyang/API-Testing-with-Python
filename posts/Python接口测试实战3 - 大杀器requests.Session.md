# Python接口测试实战3 - 接口封装的基石：requests.Session

![python_requests_module](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/nfotv.png)

笔者在面试候选人的时候，如果恰巧对方使用过`requests`库，一般会问一个问题：谈一下你对`requests.Session`对象的理解？

一个很简单的问题，但印象中几乎没有候选人回答上来过，而且很多人在使用`requests`做接口测试，但居然不知道`Session`这个对象，这让我有点匪夷所思。

难道一般的接口测试中用不到这个对象？非也，让我们配合着源码来看这段简单的代码： `requests.get("http://www.baidu.com")`

```python
def get(url, params=None, **kwargs):
    r"""Sends a GET request.
    ...
    """
    return request('get', url, params=params, **kwargs)
```

然后再翻查下`requests.requst`这个函数：

```python
def request(method, url, **kwargs):
    """Constructs and sends a :class:`Request <Request>`.
	...
	"""
    with sessions.Session() as session:
        return session.request(method=method, url=url, **kwargs)
```

**可以看到调用requests.request实际是实例化一个Session对象，而后销毁；Session.request才是真正发起请求的方法。**

*****

我们看下官方对`requests.Session`的介绍：

>The Session object allows you to persist certain parameters across requests. It also persists cookies across all requests made from the Session instance, and will use `urllib3`’s [connection pooling](https://urllib3.readthedocs.io/en/latest/reference/index.html#module-urllib3.connectionpool). So if you’re making several requests to the same host, the underlying TCP connection will be reused, which can result in a significant performance increase (see [HTTP persistent connection](https://en.wikipedia.org/wiki/HTTP_persistent_connection)).

我把其特性提炼出来：

- Session可以保持不同请求的公共参数
- Session可以保持cookies
- Session自动化维护http连接池

拿`httpbin.org`来展示下`Session`对象的cookies的自动管理：

```python
>>> import requests
>>> session = requests.Session()
>>> resp = session.get("https://httpbin.org/cookies/set", params=dict(freeform="hello world"))
>>> resp = session.get("https://httpbin.org")
>>> resp.request.headers
{'User-Agent': 'python-requests/2.18.4', 'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive', 'Cookie': 'freeform="hello world"'}
```

`Session`对象的接口自动化最大的价值时保持了client的登录态，简化了对Cookies的维护能力。很多时候，你可以把它当做一个浏览器进程：当你在某个tab页面实现了登录，在其他页面自然也登录了。

*介绍完`Session`的基本概念后，下一章就要正式开始接口自动化的实战了，尽请期待 ：）*

