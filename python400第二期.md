# 文件操作（IO技术）

## 文本文件和二进制文件

按文件中数据组织形式，我们把文件分为文本文件和二进制文件两大类。

1.文本文件

​		文本文件存储的是普通“字符”文本，python默认为 unicode 字符集（两个字节表示一个文档，最多可以表示：65536个），可以使用记事本程序打开，但是像 word 软件编辑的文档不是文本文件。

2.二进制文件

​		二进制文件把数据内容用“字节”进行存储，无法用记事本打开，必须使用专用的软件解码。常见的有：MP4视频文件、MP3音频文件、JPG图片、doc 文档等等。

## 文件操作python相关模块

- io模块  ：文件流的输入和输出操作
- os模块  ： 基本操作系统功能，包括文件操作
- glob模块  ： 查找符合特定规则的文件路径名
- fnmatch模块  ：使用模式俩匹配文件路径名
- fileinput 模块  ：处理多个输入文件
- filecmp 模块  ：处理文件的比较
- cvs 模块  ：处理csv文件处理
- pickle 和 cPickle  : 用于序列化和反序列化
- xml 包  ：用于 xml 数据处理
- bz2、gzip、zipfile、zlib、tarfile：用于处理压缩和解压缩文件（分别对用不同的算法）

## 创建文件对象 open()

open() 用于创建文件对象，基本语法格式如下：

```python
open(文件名[,打开方式])
```

如果只是文件名，代表当前目录下的文件，文件名可以录入全路径，如：D：\a\b.txt，为了减少 “\” 的输入,可以使用原始字符串：`r"d:\b.txt"`,示例如下:

```python 
f = open(r"d:\b.txt","a")
```

打开方式有如下几种：

- r  :  读模式
- w  :  写模式，如果文件不存在则创建；如果文件存在，则重写该内容
- a  :  追加模式，如果文件不存在则创建；如果文件存在，则在文件末尾追加内容
- b  :  二进制模式（可与其他模式组合使用）
- \+  :  读、写模式（可与其他模式组合使用）

文本文件对象和二进制文件对象的创建：

​		如果我们没有增加模式“b”，则默认创建的是文本文件对象，处理的基本单元是“字符”,如果是二进制模式“b”,则创建的是二进制文件对象，处理的基本单元是“字节”。

## 文本文件的写入

### 基本的文件写入操作

文本文件的写入一般就三个步骤:

1.创建文件对象

2.写入数据

3.关闭文件对象

```python
f = open("a.txt", "a")
s = "张三\n 学python\n"
f.write(s)
f.close()
```

### write()/writelines() 写入数据

- `write(a)`  :  把字符串 a 写入到文件中

- `writelines(b)` : 把字符串列表写入文件中,不添加换行符

  ```python
  f = open(r"bb.txt", "w", encoding="utf-8")
  s = ["张三\n", "李四\n", "王五\n"]
  f.writelines(s)
  f.close()
  ```

### close() 关闭文件流

​		由于文件底层是由操作系统控制,所以我们打开的文件对象必须显式调用 close() 方法关闭文件对象,当调用 close() 方法时,首先会把缓冲区数据写入文件(也可以直接调用 flush()方法),再关闭文件,释放文件对象.

​		为了确保打开的文件对象正常关闭,一般结合异常机制的 finally 或者 with 关键字实现无论何种情况都能关闭打开的文件对象.

```python
try:
    f = open(r"cc.txt", "a")
    str = "hello python!"
    f.write(str)
except BaseException as e:
    print(e)
finally:
    f.close()
```

### with 语句(上下文管理器)

 `with` 关键字(上下文管理器)可以自动管理上下文资源,不论什么原因跳出 with 快,都能确保文件正确的关闭,并且可以在代码块执行完毕后自动还原进入该代码块时的现场.

```python
s = ["张三\n", "李四\n", "王五\n"]
with open(r"dd.txt","w") as f:
    f.writelines(s)
```

## 文本文件的读取

文件的读取一般使用如下三种情况:

1.**read([size])**

​		从文件中读取 size 个字符,并作为结果返回.如果没有 size 参数,则读取整个文件.读取到文件末尾,则返回空字符串.

2.readline()

​		读取一行内容作为结果返回,读取到文件末尾,会返回空字符串.

3.readlines()

​		文本文件中,每一行作为一个字符串存入列表中,返回该列表.

```python
""" e.txt
hello python!  
System.out.println("hello world!") 
java golang  
"""
with open(r"e.txt", "r", encoding='utf8') as f:
    print(f.read(4))
```

### enumerate() 函数

```python 
with open(r"e.txt", "r") as f:
    lines = f.readlines()
    lines = [line.rstrip() + " #" + str(index) + "\n" for index, line in enumerate(lines)]
with open(r"e.txt", "w", encoding='utf-8') as f:
    f.writelines(lines)
    
输出：
"""
hello python! #0
System.out.println("hello world!") #1
java golang #2
"""
```

## 二进制文件的读取和写入

​		二进制文件的处理流程和文本文件流程一致。首先还是要创建文件对象。不过，我们需要指定二进制模式，从而创建出二进制文件对象。例如：

```python
f = open(r"d:\a.txt","wb")  #可写的，重写模式的二进制文件对象
f = open(r"d:\a.txt","ab")  #可写的，追加模式的二进制文件对象
f = open(r"d:\a.txt","rb")  #可写的二进制文件对象
```

创建好二进制文件对象后，仍然可以使用write()、read() 实现文件的读写操作。

## 文件对象的常用属性和方法

### 文件对象的属性

- name  :  返回文件的名字
- mode   :  返回文件的打开方式
- closed  ： 若文件被关闭则返回True

文件对象的常用方法

- seek(offset[,whence])  :把文件指针移动到新的位置，offset表示相对于whence的位置；

  ​		offset：off 为正网结束方向移动；为负往开始方向移动

  ​		whence不同的值代表不同的含义：

  ​			0：从文件投开始计算（默认值）

  ​			1：从当前位置开始计算

  ​			2：从文件尾开始计算

- tell()：返回文件指针的当前位置

- truncate([size]) : 不论指针在什么位置，只留下指针前 size 个字节的内容，其余全部删除；如果没有传入 size，则当指针当前位置到文件末尾内容全部删除。

- flush()：把缓冲区的内容写入文件，但不关闭文件

- close()：把缓冲区内容写入文件，同时关闭文件，释放文件对象相关资源

```python
with open("e.txt","r",encoding='utf-8') as f:
    print("文件名是：{0}".format(f.name))
    print(f.tell())
    print("读取的内容：{0}".format(str(f.readline())))
    print(f.tell())
    f.seek(0,0)
	print("读取的内容：{0}".format(str(f.readline())))
    
文件名是：e.txt
0
读取的内容：hello python! #0

18
读取的内容：hello python! #0
```

## 使用 pickle 序列化

​		python 中，一切皆对象，对象本质上就是一个”存储数据的内存块“。有时候，我们需要将”内存块的数据“保存到硬盘上，或者通过网络传输到其他的计算机上。这时，就需要”对象的序列化和反序列化“.对象的序列化机制广泛的应用在分布式、并行系统上。

​		序列化指的是：将对象转化成”串行化“数据形式，存储到硬盘或通过网络传输到其他地方。反序列化是指相反的过程，将读取到的”串行化数据“转化成对象。

序列化我们使用：

```python
pickle.dumn(obj,file)  #obj就是要被序列化的对象，file指的是存储的文件
pickle.load(file)  #从file读取数据，反序列化成对象
```

```python
import pickle

# 将对象序列化到文件
with open(r"data.dat","wb") as f:
    a = "张三"
    b = 234
    c = [20, 30, 40]

    pickle.dump(a, f)
    pickle.dump(b, f)
    pickle.dump(c, f)

    # 将获得的数据反序列化成对象
with open(r"data.dat","rb") as f:
    a1 = pickle.load(f)
    b1 = pickle.load(f)
    c1 = pickle.load(f)
    print(a1)
    print(b1)
    print(c1)
    
张三
234
[20, 30, 40]
```

## CSV文件的操作

csv 是逗号分隔符文本格式，csv文件中：

	- 值没有类型，所有的值都是字符串
	- 不能指定字体颜色等样式
	- 不能指定单元格的宽高，不能合并单元格
	- 没有多个工作表
	- 不能嵌入图像图表

### csv.reader() 对象和 csv.writerow()

```python
import csv

with open("dd.csv","r") as f:
    a_csv = csv.reader(f)
    print(a_csv)
    for row in a_csv:
        print(row)

with open("cc.csv","w") as f:
    b_csv = csv.writer(f)
    b_csv.writerow(["ID", "name", "age"])
    b_csv.writerow(["1001", "张三", "18"])

    c = [["1002", "李四", "22"], ["1003", "王五", "32"]]
    b_csv.writerows(c)
```

## os 和 os.path 模块

​		os 模块可以帮助我们直接对操作系统进行操作，我们可以直接调用操作系统的可执行文件、命令、直接操作文件、目录等等。

### os  调用操作系统文件和命令

- `os.system()` 可以帮助我们直接调用系统的命令

  ```python
  # os.system 调用Windows 系统的记事本程序
  import os
  
  os.system("notepad.exe")
  
  # os.system调用 windows 系统中的 ping 命令
  os.system("ping www.baidu.com")
  ```

- `os.startfile(path)` 直接调用可执行文件

  ```python 
  # 调用word
  os.startfile(r"C:\Program Files\Microsoft Office\root\Office16\WINWORD.EXE")
  ```

### os模块-文件和目录操作

#### os 模块下常用操作文件的方法

`remove(path)`  :  删除指定的文件

`rename(src，dest)`  :  重命名文件或目录

`stat(path)`  :  返回文件的所有属性

`listdir(path)`  :  返回 path 目录下的文件和目录列表

 #### os模块下关于目录操作的相关方法，汇总如下

`mkdir(path)`  :  创建目录

`makedirs(path1/path2/path3/...)`  :  创建多级目录

`rmdir(path)`  :  删除目录

`removedirs(path1/path2/...)`  :  删除多级目录

`getcwd()`  :  返回当前工作目录：current work dir

`chdir(path)`  :  把path 设为当前工作目录

`walk()`  :  遍历目录树

`sep()`  :  当前操作系统所使用的路径分隔符

```python
import os

# 获取文件和文件夹相关的信息
print(os.name)  # Windows -> nt linux/unix -> posix
print(os.sep)   # windows -> \  linux/unxi -> /
print(repr(os.linesep))  # windows -> \r\n linux -> \n\
print(os.stat("data.dat"))

# 关于工作目录的操作
print(os.getcwd())

nt
\
'\r\n'
os.stat_result(st_mode=33206, st_ino=4222124650660828, st_dev=3258296027, st_nlink=1, st_uid=0, st_gid=0, st_size=35, st_atime=1588390805, st_mtime=1588390805, st_ctime=1588390639)
D:\machine_learning\python400
```

### os.path模块

`os.path` 模块提供了目录相关（路径判断、路径切分、路径链接、文件夹遍历）的操作

`isabs(path)`  :  判断 path 是否绝对路径

`isdir(path)`  :  判断 path 是否为目录

`isfile(path)`  :  判断 path 是否为文件

`exists(path)`  :  判断指定路径的文件是否存在

`getsize(filename)`  :  返回文件的大小

`abspath(path)`  :  返回文件绝对路径

`dirname(p)`  :  返回目录的路径

`getatime(filename)`  :  返回文件的最后访问时间

`getmtime(filename)`  :  返回文件的最后修改时间

`walk(top，func, arg)`  :  递归方式遍历目录

`join(path, *paths)`  :  连接多个path

`split(path)`  :  对路径进行分割，以列表形式返回

`splitext(path)`  :  从路径中分割文件的扩展名

### walk() 递归遍历所有文件

os.walk() 方法：

​		返回一个三个元素的元组（dirpath，dirnames，filenames ）:

- `dirpath`  :  要列出指定目录的路径
- `dirnames`  :  目录下的所有文件夹
- `filenames`  :  目录下的所有文件

```python
import os
import os.path

all_files = []
path = os.getcwd()
list_files = os.walk(path)

for dirpath, dirnames, filenames in list_files:
    for dir in dirnames:
        all_files.append(os.path.join(dirpath, dir))
    for file in filenames:
        all_files.append(os.path.join(dirpath, file))

# 打印所有的子目录和子文件
for file in all_files:
    print(file)
```

## shutil 模块（拷贝和压缩）

​		shutil 模块是 python 标准库中提供的，主要用来做文件和文件夹的拷贝、移动、删除等；还可作文件和文件夹的压缩、解压缩。

os 模块提供了对目录或文件的一般操作，shutil 模块作为补充、提供了移动、复制、压缩、解压等操作，这些 os 模块都没有提供。

```python
import shutil
import zipfile

# 文件拷贝
shutil.copyfile("old_file", "new_file")
# 拷贝文件夹
shutil.copytree("old", "new")  # 新目录名不能存在
shutil.copytree("old", "new", ignore=shutil.ignore_patterns("*.txt", "*.html"))

# 压缩、解压缩
shutil.make_archive("压缩完后压缩包的名字", "压缩的格式（zip、tar等）", "要压缩的内容")

z1 = zipfile.ZipFile("压缩后的文件名","w")
z1.write("要压缩的文件")
z1.close()

# 解压缩
z2 = zipfileZipFile("要解压缩的文件","r")
z2.extractail("解压后的文件")
z2.close()
```

# 异常

## 异常机制本质

​		异常是指程序运行过程中出现的非正常现象。所谓异常处理就是指程序在出现问题时依然可以正确的执行剩余的程序，而不会因为异常而终止程序执行。

### python中一切都是对象，异常也采用对象的方式来处理。处理过程：

​		1.**抛出异常**：在执行一个方法时，如果发生异常，则这个方法生成代表该异常的一个对象，停止当前执行路径，并把异常对象提交给解释器。

​		2.**捕获异常**：解释器得到该异常后，寻找相应的代码来处理该异常。

## try  ... 一个except  结构

```python
try:
    被监控的可能引发异常的语句块
except BaseExecption[as e]:
    异常处理语言块
```

​		try 块包含着可能引发异常的代码，except 快则用来捕获和处理发生的异常。执行的时候，如果 try 块中没有引发异常，则跳过 except 块继续执行后续代码；执行的时候，如果 try 块中发生了异常，则跳过 try 块中的后续代码，跳到相应的 except 块中处理异常；异常处理完成后，继续执行后续代码。

```python
try:
    print("step 1")
    a = 3 / 0
    print("step 2")
except BaseException as e:
    print("step 3")
    print(e)

# 执行结果
step 1
step 3
division by zero
```

## try ... 多个 except 结构

一般建议尽量捕获可能出现的多个异常，按照**先子类后父类**的顺序。

```python
try:
    被监控的可能引发异常的语句块
except Exception1:
    异常 Exception1 的语句块
except Exception2:
    异常 Exception2 的语句块
...
except BaseException:
    处理可能遗漏的异常的语句块
```

```python
try:
    a = input("请输入一个被除数：")
    b = input("请输入一个除数：")
    c = float(a) / float(b)
    print(c)
except ZeroDivisionError:
    print("异常，不能除以0")
except ValueError:
    print("异常，不能将字符串转化成数字")
except NameError:
    print("异常，变量不存在")
except BaseException as e:
    print(e)
```

## try ... except ... else 结构

​		try ... except ... else 结构增加了”else块“。如果 try 块中没有抛出异常，则执行 else 块。如果try 块中抛出异常，则执行 except 块，不执行 else 块。

```python
try:
    a = input("请输入一个被除数：")
    b = input("请输入一个除数：")
    c = float(a) / float(b)
except BaseException as e:
    print(e)
else:
    print("除的结果是：", c)
```

## try ... except ... finally 结构

​		try ... except ... finally 结构中，finally 块无论是否发生异常都会被执行；通常用来释放try 块中申请的资源。

```python
try:
    a = input("请输入一个被除数：")
    b = input("请输入一个除数：")
    c = float(a) / float(b)
except BaseException as e:
    print(e)
else:
    print("除的结果是：", c)
finally:
    print("我是finally 中的语句，无论发生异常与否，都执行！")
```

## return 语句和异常处理问题

​		由于return 有两种作用：结束方法运行、返回值。我们一般并不把 return 放到异常处理结构中，而是放到方法最后。

```python
def f1():
    print("step 1")
    try:
        x = 3/0
    except:
        print("step 2")
        print("异常：0不能做除数")
    finally:
        print("step 3")

    print("step 4")
    return "e"  # 一般不要将return 语句放到try,except,else,finally块中，会发生一些意想不到的错误，建议放到方法最后

print(f1())
step 1
step 2
异常：0不能做除数
step 3
step 4
e
```



## 常见异常的解决

1.SyntaxError  :  语法错误

2.NameError  :  尝试访问一个没有申明的变量

3.ZeroDivisionError  :  除数为0错误（零除错误）

4.ValueError  :  数值错误

5.TypeError  :  类型错误

6.AttributeError : 访问对象的不存在的属性

7.IndexError  :  索引越界异常

8.KeyError  : 字典的关键字不存在

### 常见异常汇总

1.ArithmetricError  :  所有数值计算错误的基类

2.AssertionError  :  断言语句失败

3.AttributeError  :  对象没有这个属性

4.BaseException  :  所有异常的基类

5.DeprecationWarning  :  关于被弃用的特征的警告

6.EnvironmentError  :  操作系统错误的基类

7.EOFError  :  没有内建输入，达到 EOF 标记

8.Exception  :  常规错误的基类

9.FloatingPointError  :  浮点计算错误

10.FutureWarning  :  关于构造将来语义会有改变的警告

11.GeneratorExit  :  生成器发生异常来通知退出

12.ImportError  :  导入模块/对象失败

## with 上下文管理器

​		finally块由于是否发生异常都会执行，通常我们放释放资源的代码。其实，我们可以通过 with 上下文管理，更方便的实现释放资源的操作。

with 上下文管理的语法结构如下：

```python
with context_expr [as var]:
    语句块
```

​		with 上下文管理器可以自动管理资源，在 with 代码块执行完毕后自动还原进入该代码之前的现场或上下文。不论何种原因跳出 with 块，不论是否有异常，总能保证资源正常释放。

## traceback 模块

```python
# 使用trackback 模块打印异常信息
import traceback

try:
    print("step 1")
    num = 1/0
except:
    traceback.print_exc()
    
# 打印信息
step 1
Traceback (most recent call last):
  File "D:/machine_learning/python400/demo_56.py", line 6, in <module>
    num = 1/0
ZeroDivisionError: division by zero
    
###########将异常信息输出到指定文件#########
import traceback

try:
    print("step 1")
    num = 1/0
except:
    with open("a.txt","a") as f:  #异常信息添加到a.txt中
        traceback.print_exc(file=f)
```

## 自定义异常类

​		自定义异常类一般都是运行时异常，通常继承 Exception 或其子类即可。命名一般以 Error、Exception为后缀。

​		自定义异常由 raise 语句主动抛出。

```python
class AgeError(Exception):
    def __init__(self, errorInfo):
        Exception.__init__(self)
        self.errorInfo = errorInfo
    def __str__(self):
        return str(self.errorInfo) + "年龄错误,应该在1-150岁"


# 测试代码
if __name__ == "__main__": # 如果为true,则模块是作为独立文件运行，可以执行测试代码
    age = int(input("请输入一个年龄："))
    if age <1 or age > 150:
        raise  AgeError(age)
    else:
        print("正常的年龄")
        
输出：
请输入一个年龄：200
Traceback (most recent call last):
  File "D:/machine_learning/python400/demo_58.py", line 13, in <module>
    raise  AgeError(age)
__main__.AgeError: 200年龄错误,应该在1-150岁
```

## pycharm 开发环境的调试

进行调试的核心是设置断点，程序执行到断点时，暂时挂起，停止执行。

