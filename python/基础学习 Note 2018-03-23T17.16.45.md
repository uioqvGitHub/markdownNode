基础学习 Note 2018-03-23T17.16.45
========================

python 各种进制数
    八进制     3.0前 010  3.0后 0o10
    十六进制    0xaf
    二进制     0b1101

python 键盘输入
    input("input a number: ")
    input a number: 42
    ------'42'
python 条件分支
		x=3
        if x==2:
            print('2 equals')
        if x==3:
            print('3 equals')
        ------3 equals
-----------------------------------------
        if x==3: print('3 equals')
        ------3 equals

python 函数
	pow 像pow这种标准函数会被称为内建函数
        2**3
        ------8
        pow(2,3)
        ------8
    abs
        -10
        -------10
        abs(-10)
        ------10
    round   四舍五入/六入 受机器精度影响不一定能四舍五入
        1/2
        ------0.5
        round(1/2)
        ------0
        round(2.5555,1)
        ------2.6
python 模块
    使用import 导入模块
    import math
    math.floor(32.9)
    ------32

    math.ceil(32.0)
    ------32
    math.ceil(32.1)
    ------33

    sqrt(9)
    --Traceback (most recent call last):
      --File "<stdin>", line 1, in <module>
    --NameError: name 'sqrt' is not defined
    math.sqrt(9)
    ------3.0
	------------------------------------------相当于java的静态导入
    from math import sqrt
    sqrt(9)
    ------3.0
	------------------------------------------可以变量来引用这个函数
    foo=math.sqrt
    foo(9)
    ------3.0
	-------------------------------------------sqrt无法处理负数
    sqrt(-1)
    --Traceback (most recent call last):
      --File "<stdin>", line 1, in <module>
    --ValueError: math domain error
	-------------------------------------------cmath包
    import cmath
    cmath.sqrt(-1)
    ------1j


字符串
	>>> print(repr("hello,world"))
	#'hello,world'
	>>> print("hello,world")
	#hello,world
	>>> "hello,world"
	#'hello,world'
	repr函数会以带类型的方法改变值， print会以输出样式显示

------长字符串
	如果要写一个跨多行文本的字符串，可以用长字符串来显示，格式为用三个引号来代替普通引号
	‘’‘多行文本’‘’
	>>> '''
    ... sdfsdfsdf
    ... sdfsdf
    ... sdfdsf
    ... sdfsdf
    ... '''
    '\nsdfsdfsdf\nsdfsdf\nsdfdsf\nsdfsdf\n'

-------原始字符串
    可以按字符的原始样式来显示，不需要进行转义，最后一个字符不可以是\若想为\则应该对其转义
    r'文本'
    >>> r'\nsdfsd\sdf'
    '\\nsdfsd\\sdf'
    >>> r'\sdfsdfsdf\'
      File "<stdin>", line 1
        r'\sdfsdfsdf\'
                     ^
    SyntaxError: EOL while scanning string literal
    >>> r'sdfdsfsdf' '\\'
    'sdfdsfsdf\\'

--------Unicode字符串
    python中普通字符串是8位ascii码形式存储的，而Unicode字符串则以16们Unicode字符形式存储，在3.0中所有字符串都是以Unicode字符串来存储的
    u'文本'

    >>> u'顶戴顶戴'
    '顶戴顶戴'
    >>> '顶戴珍无可厚非‘
      File "<stdin>", line 1
        '顶戴珍无可厚非‘
                 ^
    SyntaxError: EOL while scanning string literal

--------第一章函数汇总
    file:///home/uioqv/桌面/book/python/python基础第一章函数汇总

序列Sequents
    python有六种内建序列，列表、元组、字符串、Unicode字符串、buffer对象、xrange对象，序列中的每个元素都被分配一个序号——即元素位置
    列表和元组的区别是列表可以修改，元组不可以修改
--------列表
    第一个元素是姓名，第二个元素是年龄
    >>> edward = ['Edward Gumby', 42]
    >>> edward
    ['Edward Gumby', 42]
    >>> john = ['John Smith', 50]
    >>> database = [edward, john]
    >>> database
    [['Edward Gumby', 42], ['John Smith', 50]]
    ---------索引示例
    根据序列的索引取值
    >>> edward[0]
    'Edward Gumby'
    >>> edward[1]
    42
    >>> database[0]
    ['Edward Gumby', 42]
    >>> database[-1]
    ['John Smith', 50]
    ----------分片示例
    >>> tag = '<a href="http://www.python.org">Python web site</a>'
    >>> tag[9:30]
    'http://www.python.org'
    >>> tag[32:-4]
    'Python web site'
    >>> tag[9:]
    'http://www.python.org">Python web site</a>'
    >>> tag[:30]
    '<a href="http://www.python.org'
    >>> tag[:]
    '<a href="http://www.python.org">Python web site</a>'
    -----------取最后几个元素
    >>> tag[-4:-1]
    '</a'
    >>> tag[-4:0]
    ''
    >>> tag[-4:]
    '</a>'

