## Python

### Python

Python是Guido van Rossum在20世纪90年代研发的一种现代编程语言（以一个著名的喜剧团体命名）。尽管Python并不能完美地适用于所有应用程序的开发，但它的优势使其成为许多情况下的理想选择。

熟悉传统语言的程序员会发现，Python很容易学习。包含了所有熟悉的结构，如循环、条件语句、数组等，但在Python中很多都更易于使用。原因有以下几点。

■ 类型与对象关联，而不是变量。变量可以被赋予任何类型的值，列表也可以包含许多类型的对象。这也意味着通常不需要进行强制类型转换（type casting），代码再也不用受制于预先声明的类型了。

■ Python通常可以执行更高级别的抽象操作。有一部分原因是源于Python语言的构建方式，另一部分原因是Python的发行版附带了内容丰富的标准代码库。一个下载网页的程序用两三行代码就可以写完了！

■ 语法规则非常简单。虽然成为一名专业的Python高手需要耗费很多时间和精力，但即便是初学者也能快速获取到足够的Python语法并编写出实用的代码。

### Python的短板

**Python不是速度最快的语言**

**Python的库不算最多**

**Python在编译时不检查变量类型**

**Python对移动应用的支持不足**

**Python对多处理器的利用不充分**

## Python简介

Python内置了多种数据类型，例如整数、浮点数、复数、字符串、列表、元组、字典和文件对象。这些数据类型可以通过操作符、内置函数、库函数或数据类型自带的方法进行操作。

### 数据类型

#### 数值

![image-20200514135604161](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514135604161.png)

#### 列表

![image-20200514135657874](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514135657874.png)

列表可以通过索引访问，从头开始或从末尾开始均可。还可以通过切片（slice）记法来表示列表的某个片段或切片。

![image-20200514135733329](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514135733329.png)

从头开始的索引用正数表示，从0开始❶，索引0表示第一个元素。从末尾开始的索引用负数表示，从-1开始❷，索引-1表示最后一个元素。用[m:n]可以获得一个切片❸，其中m是包含在内的起始索引，n是不包含在内的终止索引（参见表3-1）。[:n]表示切片从列表头开始，而[m:]则表示切片直至列表末尾才结束❹。

#### 元组

元组（tuple）与列表类似，但是元组是不可修改的（immutable）。也就是说，元组一旦被创建就不可被修改了。操作符（in、+、＊）和内置函数（len、max、min）对于元组的使用效果和列表是一样的，因为这几个操作都不会修改元组的元素。索引和切片的用法在获取部分元素或切片时和列表是一样的效果，但是不能用来添加、移除、替换元素。元组的方法也只有两个，即count和index。元组的重要用途之一就是用作字典的键。如果不需要修改元素，那么使用元组的效率会比列表更高。

![image-20200514135844930](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514135844930.png)

![image-20200514135901490](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514135901490.png)

#### 字符串

字符串处理是Python的一大强项。标识字符串的方式有很多种：![image-20200514135942909](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514135942909.png)

![image-20200514135958446](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514135958446.png)

#### 字典

Python内置的字典（dictionary）数据类型提供了关联数组的功能，实现机制是利用了散列表（hash table）。内置的len函数将返回字典中键/值对的数量。del语句可以用来删除键/值对。像列表类型一样，字典类型也提供了一些可用的方法（clear、copy、get、items、keys、update和values）。

![image-20200514140123793](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514140123793.png)

#### 集合

![image-20200514140154035](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514140154035.png)

#### 文件对象

![image-20200514140216184](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514140216184.png)

open语句会创建一个文件对象❶，这里以写入（"w"）模式打开当前工作目录中的文件myfile。然后写入两行数据并关闭文件❷，再以只读（"r"）模式打开该文件。os模块❸中提供了一些与文件系统相关的函数，参数是文件和目录的路径名。接着将当前目录移到另一个目录❹。但通过引用绝对路径下的文件❺，仍然可以访问到该文件。

Python还提供了其他几种输入/输出功能。内置的input函数可用来提示并读取用户录入的字符串。通过sys库模块，能访问到stdin、stdout和stderr。如果文件是由C程序生成的，或者是要供C程序访问的，那么可以通过struct库模块获得文件读取和写入的支持。用Pickle库模块则能够轻松地读写保存在文件中的Python数据类型，以实现对象数据的持久化。

### 流程控制语句结构

#### 布尔值和表达式

Python有很多表达布尔值的方式，布尔常量False、0、Python零值None、空值（如空的列表[]和空字符串""），都被视为False。布尔常量True和其他一切值都被视为True。

通过比较操作符（<、<=、==、>、>=、!=、is、is not、in、not in）和逻辑操作符（and、not、or），可以创建返回True和False的比较表达式。

#### if-elif-else语句

![image-20200514141531791](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514141531791.png)

#### while循环

![image-20200514141632368](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514141632368.png)

####  for循环

![image-20200514142200162](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514142200162.png)

#### 函数定义

![image-20200514142328698](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514142328698.png)

函数通过def语句❶来定义，并用return语句❷来返回值，返回值可以是任意类型。如果没有遇到return语句，则函数将返回Python的None值。函数的参数可以由位置或名称（关键字）来给出。在上述例子中，z和y就是按名称给出的❸。可以为函数参数定义默认值，只要在调用时不为该参数赋值即可生效❹。还可以为函数定义一个特殊的元组参数，将调用时剩余的位置参数都放入元组中❺。同样，也可以定义一个特殊的字典参数，将调用函数时剩余的关键字参数全都放入字典中❻。

#### 异常

![image-20200514142419972](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514142419972.png)

#### with

有一种更简单的try-except-finally封装模式，就是利用with关键字和上下文管理器（contextmanager）。Python会为文件访问之类的操作定义上下文管理器，开发人员也可以定义自己的上下文管理器。使用上下文管理器有一个好处，可以（通常）为其定义默认的善后清理操作，无论是否发生异常，一定能得以执行。

![image-20200514142757368](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514142757368.png)

### 创建模块

创建自己的模块十分容易，导入和使用与Python的内置库模块没有区别。代码清单3-3给出了包含了一个函数的简单模块，函数的用途是提示用户输入文件名并统计该文件中单词出现的次数。

![image-20200514142838601](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514142838601.png)

文档字符串（即docstring）是对模块、函数、方法和类给出文档说明的标准方式❶。而注释则以＃字符开头❷。read函数将返回一个字符串，其中包含了文件中的所有字符❸。字符串类的split方法将返回一个字符串列表，列表的成员是将该字符串内容按空白符分隔开的各个单词。“\”符用于将长语句拆分为多行❹。这里的if语句❺使得本段程序可以以脚本的方式运行，只要在命令行中输入python wo.py即可。

如果代码文件存放的目录被包含在模块搜索路径中（由sys.path给出），就可以像内置库模块一样用import语句导入该文件：

![image-20200514142924223](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514142924223.png)

#### 面向对象编程

Python为OOP提供了完整的支持，代码清单3-4给出了一个示例，也许可以成为一个初级的简单图形绘制模块，为某个绘图程序服务。如果对OOP比较熟悉，此例程仅供参考。对于OOP的标准特性，Python的语法和语义，与其他编程语言是类似的。

![image-20200514143336535](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514143336535.png)

类是由class关键字定义的❶。类的实例初始化方法（构造函数）叫作__init__❷，实例变量x和y就是在这里创建和初始化的❸。方法和函数一样，由def关键字定义❹。所有方法的第一个参数习惯上叫作self。当方法被调用（invoke）时，self会被置为调用该方法的实例。类Circle是从类Shape继承而来的❺，类似于一个标准的类变量❻，但又不完全相同。类必须显式调用其基类的初始化函数（initializer），这是在其初始化函数中完成的❼。__str__方法将会由print函数调用❽。类还有一些其他的特殊方法属性，用于支持操作符重载，或供len()函数之类的内置方法调用。

![image-20200514143355111](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514143355111.png)

## 基础知识

### 缩进和代码块构建

与其他大部分编程语言不一样，Python使用空白符（whitespace）和缩进来标识代码块。也就是说，循环体、else条件从句之类的构成，都是由空白符来确定的。大部分编程语言都是使用某种大括号来标识代码块的。

![image-20200514144855934](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514144855934.png)

Python不用大括号标识代码结构，而是用缩进本身来标识。上述最后两行代码就是while循环体，就是因为它们紧随while语句，并且比while语句缩进一级。如果这两行代码没做缩进，就不会构成while循环体。

采用缩进而非大括号来标识代码结构，可能需要一些时间来习惯，但却有明显的好处。

■ 不再可能有缺失或多余的大括号。再也不用一遍遍地翻看代码，只为在底部找到与前面的左括号匹配的右括号。

■ 代码结构的外观直观反映了其实际结构，看一眼就可以轻松了解代码的架构。

■ Python的编码风格能大致统一。换句话说，不太可能因为要看懂别人的古怪代码而抓狂。所有人的代码都很像是自己写的。

### 识别注释

![image-20200514144953005](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514144953005.png)

### 变量和赋值

![image-20200514145644424](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514145644424.png)

Python中的变量：是容器（bucket）还是标签（label）？

在Python中“变量”这个名称或许有点儿误导性，应该叫“名称”或“标签”会更准确一些。但是，似乎所有人都习惯称为“变量”了。无论叫什么名称，都应该知道Python中的变量是如何工作的。

对变量的常见解释就是存储值的容器，有点儿像是个桶（bucket），当然这不算精确。对许多编程语言（如C语言）来说，这种解释是合理的。

但是，Python中的变量不是容器，而是指向Python对象的标签，对象位于解释器的命名空间中。任意数量的标签（或变量）可以指向同一个对象。当对象发生变化时，所有指向它的变量的值都会改变。

Python变量可以被设为任何对象，而在C和许多其他语言中，变量只能存储声明过的类型的值。下面的Python代码是完全合法的：

![image-20200514145735323](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514145735323.png)

一开始x是指向字符串对象"Hello"的，然后又指向了整数对象5。当然，这种特性可能会遭到滥用，因为随意让同一个变量名先后指向不同的数据类型，可能会让代码变得难以理解。

### 表达式

![image-20200514145814770](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514145814770.png)

### 字符串

![image-20200514145942328](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514145942328.png)

![image-20200514150132263](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514150132263.png)

### 数值

Python没有内置更高级的数值处理函数，例如三角函数、双曲线三角函数，以及一些有用的常量，但它们都在标准模块math中提供，稍后将会详细介绍模块。现在只要知道，必须在Python程序或交互式会话中执行以下语句，才能使用本节的数学函数，这就足够了。![image-20200514150304572](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514150304572.png)

### None值

除字符串、数值等标准类型之外，Python还有一种特殊的基本数据类型，它定义了名为None的特殊数据对象。顾名思义，None用于表示空值。在Python中，None会以各种方式存在。例如，Python中的“过程”，只是一个没有显式返回值的函数，这表示默认返回的是None。

在日常的Python编程中，None经常被用作占位符，用于指示数据结构中某个位置的数据将会是有意义的，即便该数据尚未被计算出来。检测None是否存在十分简单，因为在整个Python系统中只有1个None的实例，所有对None的引用都指向同一个对象，None只等价于它自身。

### 获取用户输入

![image-20200514150355460](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514150355460.png)

### 内置操作符

Python提供了多种内置操作符，标准的操作符有+、＊等，更高级的有移位、按位逻辑运算函数等。

### Python编码风格

![image-20200514150439482](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514150439482.png)

## 列表、元组和集合

### 列表类似于数组

![image-20200514151500202](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151500202.png)

![image-20200514151518171](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151518171.png)

### 列表的索引机制

![image-20200514151541974](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151541974.png)

![image-20200514151558374](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151558374.png)

![image-20200514151610185](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151610185.png)

### 修改列表

![image-20200514151639041](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151639041.png)

![image-20200514151652060](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151652060.png)

![image-20200514151704416](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151704416.png)

![image-20200514151717626](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151717626.png)

![image-20200514151728175](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151728175.png)

### 对列表排序

![image-20200514151744564](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151744564.png)

![image-20200514151758281](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151758281.png)

![image-20200514151817997](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151817997.png)

### 其他常用的列表操作

**用in操作符判断列表成员**

![image-20200514151848582](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151848582.png)

**用+操作符拼接列表**

![image-20200514151904360](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151904360.png)

**用*操作符初始化列表**

![image-20200514151922923](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151922923.png)

**用min和max方法求列表的最小值和最大值**

![image-20200514151942640](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514151942640.png)

**用index方法搜索列表**

![image-20200514152000529](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152000529.png)

**用count方法对匹配项计数**

![image-20200514152019187](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152019187.png)

**列表操作小结**

![image-20200514152036514](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152036514.png)

### 嵌套列表和深复制

![image-20200514152056519](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152056519.png)

![image-20200514152107647](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152107647.png)

![image-20200514152119134](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152119134.png)

通过全切片（即x[:]）可以得到列表的副本，用+或＊操作符（如x+[]或x＊1）也可以得到列表的副本。但它们的效率略低于使用切片的方法。这3种方法都会创建所谓的浅副本（shallowcopy），大多数情况下这也能够满足需求了。但如果列表中有嵌套列表，那就可能需要深副本（deep copy）。深副本可以通过copy模块的deepcopy函数来得到：

![image-20200514152136737](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152136737.png)

![image-20200514152147406](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152147406.png)

### 元组

元组是与列表非常相似的数据结构。但是元组只能创建，不能修改。![image-20200514152219630](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152219630.png)

![image-20200514152233128](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152233128.png)

元组在使用句法上，有一点需要注意。因为用于包围列表元素的方括号在Python其他地方没有再用到，所以可以很清楚地用[]表示空列表，[1]则表示包含一个元素的列表。但是用于包含元组的圆括号，就并非如此了。圆括号还可以用来对表达式的内容进行分组，以便能强制按照指定顺序对表达式求值。例如，Python程序中出现了(x + y)，那么到底是先求x与y的和再放入单个元素的元组，还是在任意一侧的表达式起作用之前用圆括号先强制对x和y求和呢？

仅当元组只包含一个元素时，才会出现上述问题。因为元组内的多个元素之间会用逗号分隔，Python编译器看到逗号，就会认为这里的圆括号是用来标识元组的，而不是要将表达式内容分组。当元组只包含一个元素时，Python会要求在元素后面跟一个逗号，以便消除歧义。当元组不包含元素（空元组）时，不会有问题。一对空的圆括号肯定是一个元组，不然就毫无意义了：![image-20200514152253146](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152253146.png)

方便起见，Python允许元组出现在赋值操作符的左侧，这时元组中的变量会依次被赋予赋值操作符右侧元组的元素值。示例如下：

![image-20200514152326499](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152326499.png)

![image-20200514152339160](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152339160.png)

![image-20200514152350346](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152350346.png)

![image-20200514152404589](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152404589.png)

### 集合

![image-20200514152438037](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152438037.png)

![image-20200514152454440](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152454440.png)

## 字符串

### 字符序列

如果要提取字符或子字符串，可以将字符串看作是一系列字符，也就是说可以使用索引和切片语法进行操作：![image-20200514152958618](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514152958618.png)

但是字符串并不是字符列表。字符串和列表之间最明显的区别就是，字符串不可修改。如果尝试string.append('c')或string[0] = 'H'之类的操作，将会引发错误。在上面去除字符串中的换行符的例子中，是新建了原字符串的一个切片，而不是直接在原字符串上做修改。这是Python的一个基本限定，目的是为了提高效率。

### 基本的转义序列

![image-20200514153025570](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514153025570.png)

### 常用的字符串操作

![image-20200514153105849](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514153105849.png)

### format

![image-20200514153128760](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514153128760.png)

## 字典

■ 列表中的值可以通过整数索引进行访问，索引表示了给定值在列表中的位置。

■ 字典中的“值”通过“键”进行访问，键可以是整数、字符串或其他Python对象，同样表示了给定值在字典中的位置。换句话说，列表和字典都支持用索引来访问任意值，但是字典“索引”可用的数据类型比列表的索引要多得多。而且字典提供的索引访问机制与列表的完全不同。

■ 列表和字典都可以存放任何类型的对象。

■ 列表中存储的值隐含了按照在列表中的位置排序，因为访问这些值的索引是连续的整数。这种顺序可能会被忽略，但需要时就可以用到。存储在字典中的值相互之间没有隐含的顺序关系，因为字典的键不只是数字。注意，如果用字典的时候同时还需要考虑条目的顺序（指加入字典的顺序），那么可以使用有序字典。有序字典是字典类的子类，可从collections模块中导入。还可以用其他数据结构（通常是列表）来定义字典条目的顺序，显式地将顺序保存起来，但这不会改变普通字典没有隐式（内置）排序的事实。

## 函数

### 函数定义

![image-20200514153355540](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514153355540.png)

![image-20200514153409211](C:\Users\小硕哥\AppData\Roaming\Typora\typora-user-images\image-20200514153409211.png)

## 模块和作用域规则