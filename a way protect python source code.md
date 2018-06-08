# 浅谈Python代码的一种保护方式
## 概述
 在现有zData项目中有许多组件都是由Python开发，而WEB服务端采用流行的Python+Flask+uwsgi的开放和部署模式。而该部署模式在我们的交付过程中，存在最大的问题就是客户能完全获取并浏览zData的Python源代码。zData不能像一般的Python App那样采用已知的pyInstaller或者cx_Freeze那样打包为一个二进制可执行程序跑在uwsgi WEB容器中。现在zData采用的就是最原始的仅仅把Python源代码编译为pyc文件后同时删除py源文件，保留pyc文件，最简陋的代码保护模式。但是对于熟悉python的人来说，这种保护和没有保护没有任何区别。笔者通过深入Python的Import机制，想到一种Python代码的保护方式, 分享给大家。
## Python import机制
 Python本身提供了import语句，__import__函数来实现module的引用。当我们执行import [module]时，Python解释器会查找该module模块，并且将该模块引入到当前的工作空间，简单来说就是做了两件事:
  1.查找相应的module
  2.加载module到local namespaces
## 查找module的过程
  1.检查 sys.modules中是否导入了之前import的 module，sys.modules是一个dict，保存了已经导入到工作空间的module.如果没有找到进入第二步
  2.检查 sys.meta_path. sys.meta_path是一个list，里面保存了一些finder对象，这些finder对象实现了如何查找module。
  3.如果finder对象找到了该module，则执行相应的loader对象，load module。
  4.查找过程中，如果出现问题，抛出ImportError

以下是python module import的简略流程图
```flow
st=>start: Start
op1=>operation: 检查模块是否在sys.modules字典中
op2=>operation: 查找sys.meta_path中的finder对象
op3=>operation: 抛出ImportEroor
op4=>operation: 执行load_module操作
cond1=>condition: 模块在sys.modules字典中?
cond2=>condition: 存在合法的finder对象?
cond3=>condition: load_module操作完成?
e=>end
st->op1->cond1
cond1(yes)->e
cond1(no)->op2->cond2
cond2(yes)->op4->cond3
cond2(no)->op3->e
cond3(yes)->e
cond3(no)->op3->e
```
## 一个简单Finder对象的实现例子
```python
## I write this test example and test this script with Python3.6
## example_import.py
import importlib.abc
from importlib.machinery import ModuleSpec
import sys
import os
import py_compile
import types

class TestLoader(importlib.abc.Loader):
    def create_module(self, spec):
        return None

    def exec_module(self, module):
        fullname, _ = module.__name__.split('.')
        with open(module.__name__, 'r') as input_file:
            input_text = '\n'.join(input_file.readlines())
            code = compile(input_text, module.__name__, 'exec')
            mod = sys.modules.setdefault(module.__name__, types.ModuleType(fullname))
            mod.__file__ = module.__name__
            mod.__loader__ = self
            mod.__package__ = ''
            mod.__name__ = fullname
            exec(code, mod.__dict__)

class TestFinder(importlib.abc.MetaPathFinder):
    def find_spec(self, fullname, path, target=None):
        if os.path.exists(fullname+'.pye'):
            return importlib.util.spec_from_loader(os.path.realpath(fullname + '.pye'), TestLoader())
        else:
            raise ImportError
sys.meta_path.append(TestLoader())
```

```python
## wjy.pye
def func():
    print('test import module')
```

```python
## test.py
import example_import
import wjy

wjy.func()
## show test import module
```
## Finder对象的实现例子之解释
  笔者在example_import.py文件中实现了一个TestFinder对象，该对象会去寻找我们定义的wjy模块，但是注意该模块后缀为pye，不是标准py文件，虽然它们实际文件内容与标准python代码一样。该TestFinder对象会查找后缀为pye的文件，并且返回一个ModuleSpec对象，ModuleSpec对象为python3.4引入，这里可以简单理解为一个拥有loader对象的spec即可。它主要作用还是帮助finder对象找到对应的loader对象。

  笔者这里还实现了一个TestLoader，该TestLoader对象是我们这里实现能够解析pye文件python代码的核心，这里笔者做了简化处理，仅仅把pye文件作为纯文本读出来，并且执行compile操作，编译为python bytecode，加入到 sys.modules字典中，并且调用exec执行。

  最后把TestFinder对象加入到sys.meta_path中。在Python36中，sys.meta_path已经包含了一些默认的finder对象，在python27中，sys.meta_path为空。

  在test.py文件中，我们首先导入example_import模块，然后导入wjy模块,注意这里wjy模块实际是一个后缀为pye的文件，在没有首先导入import_example的情况下，直接导入wjy模块，会直接抛出ImportError，提示找不到该模块。而这里因为首先导入了example_import模块，有了TestFinder对象，故可以识别后缀为pye的模块，并且import wjy成功到工作区，从而能调用wjy.func()，并且可以发现在__pycache__目录下，没有生成对应的pyc文件。__pycache__目录为py代码编译为pyc文件的一个默认目录。

## 由Python Import机制想到的代码保护方式
  在以上代码中，可以发现当导入wjy模块时，这里是通过直接读文本的方式把wjy.pye文件内容读出，然后执行编译为python bytecode。如果为了达到保护python源代码的的方式，可以通过一个工具对wjy.py进行加密，加密为新的wjy.pye文件，通过编辑器直接打开，是人类不可读的乱文本。而可以通过在TestLoader加入一行解密工具，或者解密函数的调用，把wjy.pye文件内容解密为正常的python源代码。示例代码如下:
```python
class TestLoader(importlib.abc.Loader):
    def create_module(self, spec):
        return None

    def exec_module(self, module):
        fullname, _ = module.__name__.split('.')
        with open(module.__name__, 'r') as input_file:
            content = input_file.read() 
            input_text = mydecrypt(content)         #mydecript is a decrypt func or a decript tool
            code = compile(input_text, module.__name__, 'exec')
            mod = sys.modules.setdefault(module.__name__, types.ModuleType(fullname))
            mod.__file__ = module.__name__
            mod.__loader__ = self
            mod.__package__ = ''
            mod.__name__ = fullname
            exec(code, mod.__dict__)
```
  通过以上步骤即可做到一定程度保护python源代码。
## 该python代码加密保护方式的优点和局限性
  笔者提出的python代码加密保护方式的优点:方案实现简单，容易实现，并且在此基础上还可以扩展出许多功能，比如在导入模块时加入licenses的实现或者远程认证等，但是需要python程序员对python import module机制比较熟悉。

  但是笔者提出的方案也有缺点，虽然用户不能直接通过编辑器浏览python源代码了，但是可以通过直接在TestLoader对象中打断点或者标准输出的方式，在代码执行过程中，把代码获取到，虽然这样浏览代码的成本很高，但是这已经加大了用户访问，理解源代码的成本。更好的做法，当然是用c/c++实现该finder和loader对象，并且加入到sys.meta_path字典中，用户就没办法通过打断点的方式，读到任何python源代码了。
## 总结
  希望坚持阅读到本处的读者，能理解Python import机制，并且理解了笔者如何通过python import 机制实现python源代码的加密保护。笔者在准备此文章时仓促，难免会有错误，欢迎大家指正。
## 附录
  笔者在写此文章，难免会去做一些项目或者文章的参考与引用，以下是参考文章或者项目的目录
  https://github.com/Liuchang0812/slides/tree/master/pycon2015cn
  https://github.com/dashingsoft/pyarmor
  https://docs.python.org/3/library/importlib.html
  https://www.python.org/dev/peps/pep-0302/