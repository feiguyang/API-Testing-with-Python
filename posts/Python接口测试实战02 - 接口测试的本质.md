# Python接口测试实战02 - 接口测试的本质

在上一篇简单的介绍了接口测试里用到的工具、类库后，本篇正式开始讲用Python进行接口测试实战。

### 1. 从一个登录接口了解接口调用的方法

现在假设有一个登录接口`POST /api/login`，该接口有两个入参`username`和`password`，分别表示帐号的用户名以及密码，那该如何调用该接口呢？

_以下该登录接口都以一个临时的url:`http://requestbin.qahub.net/z3m6v8z3`来替代_

#### 1.1 curl做接口调用：

先用curl来做一个简单的例子：

```bash
>> curl -X POST -d "username=foo&password=bar%21" http://requestbin.qahub.net/z3m6v8z3

ok
```

在`http://requestbin.qahub.net/z3m6v8z3?inspect`上查看下本次请求的报文：

![](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/aw2xk.png)

#### 1.2 Postman调用接口：

然后用**Postman**调用同一个接口：

![](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/smfhu.png)

再在页面上查看下本次请求的详情：

![](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/nowuz.png)

`Body`部分完全一致，只不过请求头里添加了一堆额外的信息，但并不影响。

#### 1.3 `requests`调用接口：

最后用python+requests这种方式来调用该接口：

![](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/wb406.png)

同样也要在web页面上确认下请求报文：

![](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/6toor.png)

### 2. 事实永远不可能如此简单：

在这个需求中，从上面三种调用方式来看的话，显然`Postman`是最简单的。

**然而，现实场景中的登录接口怎么可能如此简单？**

#### 2.1 如果请求报文是json格式：

现在`json`格式的请求报文可能更加的流行，所以如果要将该接口改成`json`要怎么办？（_curl以下略过不讲了_）

**先看下Postman:**

![](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/l2enz.png)

嗯没错，要裸写`json`字符串，千万要注意格式：别多了个逗号、少了个花括号什么的

![](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/8c1d8.png)

**再对比下使用requests**

![image-20180520122157881](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/ggusq.png)

![](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/6d9kr.png)

效果也达到了，但好像也不是很简单？ 那么再看看直接利用`json`这个参数来实现：

![image-20180520122338257](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/1fpeu.png)

这样来好像是比**Postman**简单了一些？

#### 2.2 你居然用明文传输密码？

我觉得上面举个里很差的栗子，如果服务端真的这么实现这个接口，客户不找你拼命才怪，所以我再完善下需求：**该接口接收的password字段需要md5加密后传入。**

然后我又打开了Postman:

![image-20180520123006201](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/vet6p.png)

![黑人问号脸](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/h4zrm.jpg)

突然不知道该怎么进行了。。。（_实际可以利用Postman的脚本机制完成md5的计算_）

还是用我擅长的`requests`来解决吧：

```python
import requests
import hashlib


def md5(content, encoding="utf-8"):
    m = hashlib.md5()
    m.update(content.encode(encoding))
    return m.hexdigest()


requests.post("http://requestbin.qahub.net/z3m6v8z3", json=dict(username="foo", password=md5("bar!")))
```

![](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/hk92i.png)

正当笔者对解决了md5加密问题感到欣喜的时候，

开发小哥跑过来了：**『md5加密太不安全了，要加盐或者换成AES、DES，甚至接入OAuth』**

我：**fxxk**

### 3. 接口测试的本质

#### 3.1 别忘了功能测试：

我们先看下一个传统的web登录控件，一个用户名输入框、一个密码输入框以及一个登录按钮：

![](http://my-storage.oss-cn-shanghai.aliyuncs.com/ipic/zy8xf.png)

回到我们做着功能测试的日子里，你大概会写下这样的测试用例：

| 测试用例                         | 期望结果 |
| -------------------------------- | -------- |
| 使用正确的帐号、密码登录         | 登录成功 |
| 登录的帐号有效但是密码错误       | 登录失败 |
| 帐号不存在                       | 登录失败 |
| 正确的帐号、密码，但该帐号被禁用 | 登录失败 |

可以看到：传统的功能测试，关注的是『username』和『password』两个入参条件的组合，它并不关心前后端传输的协议、报文格式以及密码字段的加密方法。

#### 3.2 接口测试的本质：

前面两个章节用一堆图讲的仅仅是**接口调用**的办法，这并不是**接口测试**。

**笔者认为：接口测试的本质就是功能测试（或者叫做业务测试），关注的应当是用高效的条件组合来验证尽可能多的业务逻辑。将功能测试从传统的UI层下沉到接口层，是为了提高测试的稳定性以及效率，并且将UI、接口层的测试目标解耦。**

接口用例的设计思想主要还是要借鉴功能测试，不过需要增加一部分接口层额外的特性，比如接口安全、数据结构特性等。协议、报文格式、数字签名等内容，应当在框架层面去解决掉，要对接口用例设计者、使用者透明。

所以上面的用例转成接口用例，我会这么来实现：

```python
import requests
import hashlib
import unittest


def md5(content, encoding="utf-8"):
    m = hashlib.md5()
    m.update(content.encode(encoding))
    return m.hexdigest()


class SampleClient:

    def __init__(self):
        self.session = requests.session()

    def login(self, username, password):
        return self.session.post("http://requestbin.qahub.net/z3m6v8z3",
                                 json=dict(username=username, password=password)).text


class TestLogin(unittest.TestCase):

    def setUp(self):
        self.client = SampleClient()

    def test_correct_account(self):
        self.assertEqual(self.client.login("foo", "bar!"), "ok")

    def test_wrong_password(self):
        self.assertNotEqual(self.client.login("foo", "bar!"), "ok")

    def test_account_not_exists(self):
        self.assertNotEqual(self.client.login("404", "bar!"), "ok")

    def test_bad_status(self):
        self.assertNotEqual(self.client.login("user-forbidden", "bar!"), "ok")

```

资深的测试工程师将接口的调用封装成具体的`Client`，普通的测试工程师只需要关注的这个`Client`下`login`的入参，结合实际的业务场景来设计入参的组合条件，并做出的准确的断言（验证期望值与实际值）。

