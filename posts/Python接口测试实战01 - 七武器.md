# Python接口测试实战01 - 七种武器



工欲善其事必先利其器，在正式开始介绍使用Python做接口测试前，我们先准备好相应的工具，这样就能快速上手接口测试了。

### 0. Python

在测试领域Python其实大有可为，不管是Web、移动端、接口、嵌入式测试都能看到Python的身影。而且因为Python语法的简单性，用Python做自动化测试的效率极高。

有人可能会说：**Python运行效率低啊。**可是你想想，在自动化测试应用时，我们需要在乎底层脚本语言的运行效率吗？与其在乎运行效率，还不如思考下如何快速提高自动化测试的覆盖率，也就是自动化case的开发效率。

Python有Python2以及Python3两个大版本，[Python2行将就木](https://pythonclock.org/)，本系列文章都以Python3为基础，而且有可能会使用到Python3.5以上版本的特性。

- 官网：https://www.python.org
- Windows版下载地址: https://www.python.org/downloads/windows/

### 1. pip && pipenv

[pip](https://pypi.org/project/pip) 是python下最常用的包管理工具，安装`pip`之后能够通过运行`pip install [packageName]`快速安装各种依赖。

[pipenv](https://docs.pipenv.org/) 是个更先进的包管理工具，补强了`pip`，不过要安装它，得先安装`pip`:) ，笔者更推荐大家使用`pipenv`来管理项目的依赖。

安装方法：

```bash
pip install pipenv
```

>☤ Pipenv Features
>
>- Enables truly *deterministic builds*, while easily specifying *only what you want*.
>- Generates and checks file hashes for locked dependencies.
>- Automatically install required Pythons, if `pyenv` is available.
>- Automatically finds your project home, recursively, by looking for a `Pipfile`.
>- Automatically generates a `Pipfile`, if one doesn’t exist.
>- Automatically creates a virtualenv in a standard location.
>- Automatically adds/removes packages to a `Pipfile` when they are un/installed.
>- Automatically loads `.env` files, if they exist.



### 2. pyenv

[pyenv](https://github.com/pyenv/pyenv)是python多版本管理工具，尤其推荐`Linux`或者`Mac`用户使用：

- 系统自带Python版本过低，而且升级风险较大
- 如不能正确的管理系统Python的依赖，可能对系统内置工具的使用造成影响

所以笔者建议每位非Windows用户最好都安装`pyenv`，并下载一个较新的版本进行安装。

![pyenv](https://raw.githubusercontent.com/pyenv/pyenv/master/terminal_output.png)

### 3. Pycharm

[Pycharm](https://www.jetbrains.com/pycharm/)应该是世界上最好的Python IDE吧，而且社区版还是免费，还不下载个？

![pycharm](https://www.jetbrains.com/pycharm/img/screenshots/simpleLook@2x.jpg)

**下载地址：**

https://www.jetbrains.com/pycharm/download/

### 4. Unittest

`unittest`是Python内置的单元测试框架，它与junit这类单元框架很相似，之后的文章里笔者会使用该框架来组织接口测试用例。

**官方文档**：

https://docs.python.org/3/library/unittest.html

### 5. Requests

`requests`是Python的一个第三方HTTP库，然后有可能是这个星球上最好用的http库了。举个栗子，笔者要发起一个POST请求，请求报文为JSON字符串，那只要这么做：

```python
requests.post("http://example.com/login", json={"username": "foo", "password": "bar!"})
```

![requests](http://docs.python-requests.org/zh_CN/latest/_static/requests-sidebar.png)

**在线安装**: `pipenv install requests`

**官方文档：**http://docs.python-requests.org/en/master/

### 6. pytest

pytest 是python的第三方测试框架，相比`unittest`它增强了很多功能，但是笔者对它的使用上相对谨慎，之后只会在用例的收集、执行层上使用它。

![pytest](https://docs.pytest.org/en/latest/_static/pytest1.png)

**在线安装**: `pipenv install pytest`

**官方文档:** https://docs.pytest.org/en/latest/contents.html