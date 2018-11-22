6. 模块
=======

当你退出Python解释器再进入时，你所定义的内容(函数和变量)都会丢失。因此，当你需要写更庞大的程序时，你最好通过文本编辑器来准备解释器的输入，并以该文件作为解释器的输入来执行。这就是创建 *脚本* 。当你的程序变得更大时，你会想要将脚本划分为多个文件以便于维护。你也会想要在多个程序中使用同一个你写的便利的函数，并且不需要将函数的定义拷贝到每一个程序中

为了支持这些功能，Python提供了一个方法，将定义放在一个文件中并可以在脚本或解释器交互模式中使用。这个文件就叫做 *模块* ；模块中的定义可以被导入到其他模块或 *主模块* 中(the collection of variables that you have access to in a script executed at the top level and in calculator mode)。

模块就是包含了Python定义和语句的文件。这个文件的名字就是模块名加上 ``.py`` 的后缀。在模块中，模块名(字符串)和全局变量 ``__name__`` 的值相同。例如，用你最喜欢的文本编辑器在当前目录创建叫 ``fibo.py`` 的文件，并输入以下内容：::

    # Fibonacci numbers module

    def fib(n):  # write Fibonacci series up to n
        a, b = 0, 1
        while b < n:
            print b
            a, b = b, a + b

    def fib2(n):  # return Fibonacci series up to n
        result = []
        a, b = 0, 1
        while b < n:
            result.append(b)
            a, b = b, a + b
        return result

现在在Python解释器中通过下面的指令导入该模块：

    >>> import fibo

这里没有直接输入 ``fibo`` 模块当前符号表中的函数名；只是输入了模块名 ``fibo``。使用模块名可以访问函数：

    >>> fibo.fib(1000)
    1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987
    >>> fibo.fib2(100)
    [1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
    >>> fibo.__name__
    'fibo'

如果你打算经常使用某个函数，你可以将函数赋予一个局部变量：

    >>> fib = fibo.fib
    >>> fib(500)
    1 1 2 3 5 8 13 21 34 55 89 144 233 377

=============
6.1. 深入模块
=============

模块可以包含可执行语句和函数定义。这些语句用于初始化模块。这些语句只有当模块名 *第一次* 出现在导入语句中时才会被执行。[#f1]_ (当文件被当作脚本执行时，这些语句也会被执行。)

每个模块都有其私有的符号表，该表用作模块中所有函数定义的全局模块表。所以，模块的作者可以在模块中使用全局变量而不用担心和用户的全局变量造成意外冲突。另一方面，如果你能确定知道自己在做什么，你可以通过和访问模块函数相同的表示法来访问模块的全局变量，如 ``modname.itemname``。

模块可以导入其他模块。将 ``import`` 语句放在模块(或脚本)的开头这种做法，是属于习惯而不是必需的要求。被导入的模块名会被放置于当前模块的全局符号表中。

以下是导入语句的一种变体，直接从模块中导入多个命名到当前模块符号表。例如：

    >>> from fibo import fib, fib2
    >>> fib(500)
    1 1 2 3 5 8 13 21 34 55 89 144 233 377

这并不会引入模块名到当前的符号表(因此在上面的例子中，``fibo`` 并没有被定义)

以下也是导入语句的一种变体，导入模块定义的所有命名：

    >>> from fibo import *
    >>> fib(500)
    1 1 2 3 5 8 13 21 34 55 89 144 233 377

这会导入所有除了以下划线(_)开头的命名。

需要注意的是，从模块或包中导入*的做法一般都是不推荐的，这会降低代码的可读性。然而在交互模式中使用这种方法来减少输入是可接受的。

如果模块名后面跟着 ``as``，``as`` 后面的名字会直接和导入的模块绑定。

    >>> import fibo as fib
    >>> fib.fib(500)
    0 1 1 2 3 5 8 13 21 34 55 89 144 233 377

实际上，除了模块被命名为 ``fib`` 以外，这和通过 ``import fibo`` 来导入模块所做的事情是一样的。

使用 ``from`` 时也可以以此来达到类似的效果：

    >>> from fibo import fib as fibonacci
    >>> fibonacci(500)
    0 1 1 2 3 5 8 13 21 34 55 89 144 233 377

.. note::

   出于效率的考虑，每个模块在每个解释器会话中只会被导入以此。所以，如果你改变了你的模块，你必须重启解释器——或者，如果你想交互式地测试某个模块，使用 ``reload()``，例如，``reload(modulename)``。

-------------------------
6.1.1. 作为脚本来执行模块
-------------------------

当你通过下面的语句执行Python模块时

    python fibo.py <arguments>

就像你导入他时一样，模块中的代码会被执行，同时 ``__name__`` 会被设为 ``"__main__"``。这意味着通过在模块的结尾加上下面的代码

    if __name__ == "__main__":
        import sys
        fib(int(sys.argv[1]))

由于只有当这个模块作为“main”文件被执行时这段代码才会解析命令行，你就能使该文件可作为脚本被调用，也可作为模块被导入：

    $ python fibo.py 50
    1 1 2 3 5 8 13 21 34

如果模块被导入，这段代码就不会运行：

    >>> import fibo
    >>>

这通常用来为模块提供方便的用户接口，或用于测试目的(将模块作为脚本运行来执行测试)。

-------------------
6.1.2. 模块搜索路径
-------------------

当导入命名为 **spam** 的模块时，解释器先在内置模块中搜索该命名。如果没找到，则会从变量 **sys.path** 所包含的目录列表中搜索命名为 ``spam.py`` 的文件。**sys.path** 通过下面这些位置来初始化：

* 包含输入脚本的目录(或当前目录)。
* **PYTHONPATH** (一个目录命名的列表，和shell变量 **PATH** 使用相同语法)。
* Python默认安装目录

初始化后，Python程序可以修改 **sys.path**。包含被执行脚本的目录出于搜索路径的首位，优先于标准库路径。这意味着该目录下的脚本会代替库目录下的同名模块被加载。除非这个替换是故意的，不然这就是个错误。查看 标准模块_ 以了解更多信息。

-------------------------
6.1.3. “编译”Python文件
-------------------------

对于使用了大量标准库的简单程序，有一个提升启动时间的重要方法，如果 ``spam.py`` 所在目录下存在 ``spam.pyc`` 文件，则该文件被视为模块 **spam** 的“字节编译”版本。用于创建 ``spam.pyc`` 而产生的 ``spam.py`` 的修改时间会被记录在 ``spam.pyc`` 文件中，当两个修改时间不匹配时，``.pyc`` 被忽略。

通常你不需要做任何事情去创建 ``spam.pyc`` 文件。当 ``spam.py`` 成功编译时，Python会尝试将编译后的字节写入 ``spam.pyc`` 中。尝试失败并不算是错误；如果因为任何原因该文件没有完整写入，``spam.pyc``文件会被识别为无效并在稍后的流程中被忽略。``spam.pyc`` 文件的内容是无关平台的，所以Python模块目录可以在不同架构的机器间分享。

一些高级技巧：

* 以-o参数调用Python解释器时，会生成优化代码并存储到 ``.pyo``中。优化器目前并没有帮上太多忙；它仅仅移除了 **assert** 语句。当使用-o参数时，所有字节码都会被优化；``.pyc`` 文件会被忽略，同时 ``.py`` 文件会被编译成优化后的字节码。

* 向Python解释器传递两个-o会使字节码编译器执行优化，在一些罕见的情况下这会产生错误的程序。目前只会从字节码中移除 ``__doc__`` 字符串，以产出更紧凑的 ``.pyo`` 文件。由于某些程序可能依赖于这些程序的可用性，因此只有当你知道你在做什么时才应该使用该选项。

* 从 ``.pyc`` 或 ``.pyo`` 文件中读取并不会比从 ``.py`` 文件中读取使程序运行得更快；只有在加载时，``.pyc`` 或 ``.pyo`` 文件提供更快的速度。

* 当脚本通过文件名在命令行中运行时，脚本的字节码不会写入到 ``.pyc`` 或 ``.pyo`` 文件中。所以，脚本的启动时间可以通过将大部分代码移到一个模块中并保持较少的启动代码来导入该模块来减少。也可以直接在命令行中指定一个 ``.pyc`` 或 ``.pyo`` 文件。

* 对于同一个模块，有可能只有 ``spam.pyc`` 文件(使用-o参数时创建 ``.pyo`` 文件)，而没有 ``spam.py`` 文件。这可以用于发布比较难于逆向工程的Python代码库。

* **compileall** 模块可以为目录中所有模块创建 ``.pyc`` 文件(使用-o参数时创建 ``.pyo`` 文件)。

.. _标准模块:

=============
6.2. 标准模块
=============

Python带有一个标准模块库，通过叫做Python库参考手册(此后成为库参考手册)独立文档描述。一些模块内置于解释器中；这些操作提供对不属于语言核心但仍然内置的操作的访问，以提高效率或提供对系统调用等操作系统原语 *(译注：原文为operating system primitives)* 的访问。这些模块的集合是一个配置选项，它也取决于底层平台。例如，**winreg** 模块就只Windows平台下提供。有一个特别的模块需要注意：**sys**，它内置于所有Python解释器。变量 ``sys.ps1`` 和 ``sys.ps2`` 定义了主提示符和副提示符字符串：

    >>> import sys
    >>> sys.ps1
    '>>> '
    >>> sys.ps2
    '... '
    >>> sys.ps1 = 'C> '
    C> print 'Yuck!'
    Yuck!
    C>

这两个变量只有在交互模式下才会被定义。

变量 ``sys.path`` 是解释器模块搜索路径的字符串列表。它由环境变量 **PYTHONPATH** 初始化，如果没有设定 **PYTHONPATH** ，就由内置的默认值初始化。你可以通过标准的列表操作来修改它：

    >>> import sys
    >>> sys.path.append('/ufs/guido/lib/python')

===================
6.3. **dir()** 函数
===================

内置函数 **dir()** 用于找出模块中定义的所有命名。它返回一个排序的字符串列表：

    >>> import fibo, sys
    >>> dir(fibo)
    ['__name__', 'fib', 'fib2']
    >>> dir(sys)
    ['__displayhook__', '__doc__', '__excepthook__', '__name__', '__package__',
    '__stderr__', '__stdin__', '__stdout__', '_clear_type_cache',
    '_current_frames', '_getframe', '_mercurial', 'api_version', 'argv',
    'builtin_module_names', 'byteorder', 'call_tracing', 'callstats',
    'copyright', 'displayhook', 'dont_write_bytecode', 'exc_clear', 'exc_info',
    'exc_traceback', 'exc_type', 'exc_value', 'excepthook', 'exec_prefix',
    'executable', 'exit', 'flags', 'float_info', 'float_repr_style',
    'getcheckinterval', 'getdefaultencoding', 'getdlopenflags',
    'getfilesystemencoding', 'getobjects', 'getprofile', 'getrecursionlimit',
    'getrefcount', 'getsizeof', 'gettotalrefcount', 'gettrace', 'hexversion',
    'long_info', 'maxint', 'maxsize', 'maxunicode', 'meta_path', 'modules',
    'path', 'path_hooks', 'path_importer_cache', 'platform', 'prefix', 'ps1',
    'py3kwarning', 'setcheckinterval', 'setdlopenflags', 'setprofile',
    'setrecursionlimit', 'settrace', 'stderr', 'stdin', 'stdout', 'subversion',
    'version', 'version_info', 'warnoptions']

不带参数时，**dir()** 列出当前定义的命名：

    >>> a = [1, 2, 3, 4, 5]
    >>> import fibo
    >>> fib = fibo.fib
    >>> dir()
    ['__builtins__', '__name__', '__package__', 'a', 'fib', 'fibo', 'sys']

需要注意的是，它列出所有类型的命名：变量、模块、函数等。

**dir()** 不列出内置的函数和变量。如果你想要内置模块的列表，它们都定义于标准模块 **__builtin__**：

    >>> import __builtin__
    >>> dir(__builtin__)
    ['ArithmeticError', 'AssertionError', 'AttributeError', 'BaseException',
    'BufferError', 'BytesWarning', 'DeprecationWarning', 'EOFError',
    'Ellipsis', 'EnvironmentError', 'Exception', 'False', 'FloatingPointError',
    'FutureWarning', 'GeneratorExit', 'IOError', 'ImportError', 'ImportWarning',
    'IndentationError', 'IndexError', 'KeyError', 'KeyboardInterrupt',
    'LookupError', 'MemoryError', 'NameError', 'None', 'NotImplemented',
    'NotImplementedError', 'OSError', 'OverflowError',
    'PendingDeprecationWarning', 'ReferenceError', 'RuntimeError',
    'RuntimeWarning', 'StandardError', 'StopIteration', 'SyntaxError',
    'SyntaxWarning', 'SystemError', 'SystemExit', 'TabError', 'True',
    'TypeError', 'UnboundLocalError', 'UnicodeDecodeError',
    'UnicodeEncodeError', 'UnicodeError', 'UnicodeTranslateError',
    'UnicodeWarning', 'UserWarning', 'ValueError', 'Warning',
    'ZeroDivisionError', '_', '__debug__', '__doc__', '__import__',
    '__name__', '__package__', 'abs', 'all', 'any', 'apply', 'basestring',
    'bin', 'bool', 'buffer', 'bytearray', 'bytes', 'callable', 'chr',
    'classmethod', 'cmp', 'coerce', 'compile', 'complex', 'copyright',
    'credits', 'delattr', 'dict', 'dir', 'divmod', 'enumerate', 'eval',
    'execfile', 'exit', 'file', 'filter', 'float', 'format', 'frozenset',
    'getattr', 'globals', 'hasattr', 'hash', 'help', 'hex', 'id', 'input',
    'int', 'intern', 'isinstance', 'issubclass', 'iter', 'len', 'license',
    'list', 'locals', 'long', 'map', 'max', 'memoryview', 'min', 'next',
    'object', 'oct', 'open', 'ord', 'pow', 'print', 'property', 'quit',
    'range', 'raw_input', 'reduce', 'reload', 'repr', 'reversed', 'round',
    'set', 'setattr', 'slice', 'sorted', 'staticmethod', 'str', 'sum', 'super',
    'tuple', 'type', 'unichr', 'unicode', 'vars', 'xrange', 'zip']

=======
6.4. 包
=======

待完成。


.. rubric:: 脚注

.. [#f1] 事实上函数定义也是“被执行”的“语句”；模块级函数定义的执行会将函数名添加到模块的全局符号表中。
