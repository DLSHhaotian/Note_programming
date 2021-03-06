# C++ tips

## C++ std::move()

在C++11中，标准库在<utility>中提供了一个有用的函数std::move，std::move并不能移动任何东西，它唯一的功能是将一个左值强制转化为右值引用，继而可以通过右值引用使用该值，以用于移动语义。从实现上讲，std::move基本等同于一个类型转换：static_cast<T&&>(lvalue);

std::move函数可以以非常简单的方式将左值引用转换为右值引用。(左值 右值 引用 左值引用)概念 https://blog.csdn.net/p942005405/article/details/84644101

1. C++ 标准库使用比如vector::push_back 等这类函数时,会对参数的对象进行复制,连数据也会复制.这就会造成对象内存的额外创建, 本来原意是想把参数push_back进去就行了,通过std::move，可以避免不必要的拷贝操作。
2. std::move是将对象的状态或者所有权从一个对象转移到另一个对象，只是转移，没有内存的搬迁或者内存拷贝所以可以提高利用效率,改善性能.。
3. 对指针类型的标准库对象并不需要这么做.

## 数学计算函数

```c++
 double log10()
 double fmod(double a,double b) 计算浮点数的余数，就是看小数位后面的数字，可以用来判断整数
```

int rand() 生成随机数

## accumulate

求和函数，做算法题的时候使用比较方便

```c++
int sum = accumulate(vec.begin() , vec.end() , 42);  第三个参数就是多加的东西
string sum = accumulate(v.begin() , v.end() , string(" "));  
```

## 二分查找的函数

```c++
#include <algorithm>
```

二分查找的函数有 3 个：

#### lower_bound

(起始地址，结束地址，要查找的数值) 返回的是数值 **第一个** 出现的位置。

功能：函数lower_bound()在first和last中的前闭后开区间进行二分查找，返回**大于或等于val的第一个元素位置**。如果所有元素都小于val，则返回last的位置.

注意：如果所有元素都小于val，则返回last的位置，且last的位置是**越界**的！！

#### upper_bound

(起始地址，结束地址，要查找的数值) 返回的是 第一个大于待查找数值 出现的位置。

功能：函数upper_bound()返回的在前闭后开区间查找的关键字的上界，返回**大于val**的第一个元素位置

注意：返回查找元素的最后一个可安插位置，也就是“元素值>查找值”的第一个元素的位置。同样，如果val大于数组中全部元素，返回的是last。(注意：数组下标越界)

#### binary_search

(起始地址，结束地址，要查找的数值)  返回的是是否存在这么一个数，是一个**bool值**。

注意：使用二分查找的前提是数组有序

使用的时候要小心，但可以看出lower和upper的区分就是判断条件>=还是>

## 求前缀和的方便函数 partial_sum

```c++
#include <numeric> 头文件
template <class InputIterator, class OutputIterator, class BinaryOperation>
OutputIterator partial_sum (InputIterator first, 
                            InputIterator last,
                            OutputIterator result, 
                            BinaryOperation binary_op);
```

**partial_sum 对于序列 a,b,c,d 产生序列 a,a+b,a+b+c,a+b+c+d。**

output的容器一定要足够大才行，也可以用input的first

## C++ 位操作

异或 相同false 不同true

与，或正常

两个数字按照二进制，对每一位进行比较

位运算是指按二进制进行的运算。在程序中，常常需要处理二进制位的问题。C/C++语言提供了6个位操作运算符。这些运算符只能用于整型操作数，即只能用于带符号或无符号的char,short,int与long类型。

在实际应用中，建议用unsigned整型操作数，因为带符号操作数可能因为不同机器结果不同。

x ^ 0s = x             x & 0s = 0           x | 0s = x
x ^ 1s = ~x           x & 1s = x           x | 1s = 1s
x ^ x = 0               x & x = x             x | x = x  

返回的值不止0，1。比较所有位之后得到结果

异或（`xor`）是一种非常常见的位运算，其计算特点是“相同为0，不同为1”，计算符号是⊕，有时也用`a^b`表示。一般使用异或时会用到下面的规律：

1. 任何数和本身的异或为0
2. 任何数与0的异或结果是自身
3. 异或满足交换律，`a^b^c = c^b^a`

有了这些基本概念之后就能尝试去解决这个题目了。

**因为异或并不是对十进制的，是对二进制的所有位进行计算的**

位操作，左移<<，右移>> 对变量进行位操作，并不会改变这个变量的值，只有通过赋值才能得到移动后的数值。

```c++
a>>2 a不变
a>>=2 a才是变了
```

1.反转的技巧，左移右移用&1

2.异或的技巧 交换律和^0

1. 交换律：a ^ b ^ c <=> a ^ c ^ b
2. 任何数于0异或为任何数 0 ^ n => n
3. 相同的数异或为0: n ^ n => 0

```c++
a = [2,3,2,4,4]
2 ^ 3 ^ 2 ^ 4 ^ 4等价于 2 ^ 2 ^ 4 ^ 4 ^ 3 => 0 ^ 0 ^3 => 3
```

## char* char[] 和string

```c++
char* s="abcds";
```

abcds是一个常量，声明一个字符指针指向他的第一位

但之后对s无法操作，因为它指向的是个常量！s[0]='f';就是错的

```c++
char s[3]={'a','b'};要多一个空间装终止符
char s[]="abcdehf";这里就不用写多少个了，编译器会自动帮你做
```

这个就可以进行操作了，因为在栈上开辟了空间给s

### malloc&free

这里引申到另一种的声明方法，用动态申请内存

malloc的全称是memory allocation，中文叫动态内存分配。

```c++
void *malloc(int size); 
```

说明：malloc 向系统申请分配指定size个字节的内存空间，返回类型是 void* 类型。void* 表示未确定类型的指针。C,C++规定，void* 类型可以强制转换为任何其它类型的指针。 　　
备注：void* 表示未确定类型的指针，更明确的说是指申请内存空间时还不知道用户是用这段空间来存储什么类型的数据（比如是char还是int或者...）

```c++
void free(void *FirstByte)：
```

 该函数是将之前用malloc分配的空间还给程序或者是操作系统，也就是释放了这块内存，让它重新得到自由。

然后注意右malloc，后面必须free掉，而且一对一。**释放后应该把指向这块内存的指针指向NULL**

```c++
char* s=(char*)malloc(10+1); 如果想要是个字符的长度，后面加个终止符的空间，因为很多函数会要求识别终止符
```

需要说明的是:字符数组并不要求它的最后一个字符为′\0′,甚至可以不包含′\0′｡ 如以下这样写完全是合法的:

```c++
char str[5]={′C′,′h′,′i′,′n′,′a′};
```

是否需要加′\0′,完全根据需要决定｡但是由于C++编译系统对字符串常量自动加一个′\0′｡因此,人们为了使处理方法一致,便于测定字符串的实际长度,以及在程序中作相应的处理,在字符数组中有效字符的后面也人为地加上一个′\0′｡

### new&delete

动态创建对象时，**只需指定其数据类型，而不必为该对象命名**，new表达式**返回指向该新创建对象的指针**，我们可以**通过指针来访问此对象**。

那么C++的这个动态申请和C的动态申请的区别主要是

1. **new 返回指定类型的指针，并且可以自动计算所需要大小。**而 **malloc 则必须要由我们计算字节数，并且在返回后强行转换为实际类型的指针。**
2. 对于**非内部数据类型的对象**而言，光用maloc/free无法满足动态对象的要求。**对象在创建的同时要自动执行构造函数，对象在消亡之前要自动执行析构函数。**由于malloc/free是库函数而不是运算符，不在编译器控制权限之内，不能够把执行构造函数和析构函数的任务强加于malloc/free。



## Reverse

**函数功能**：将序列[first,last)的元素在原容器中颠倒重排，包含在algorithm库中。

- reverse()函数无返回值，时间复杂度O(n)。
- 可以看到函数中是last是先减一。应当理解的是vector.end()是指向数组最后一个元素后面的位置。
- reverse(v.begin(), v.end())即可实现全数组的反转。

## 递归和迭代

其实在实现算法中，一般递归和迭代都可以完成目的。但是各有各的优点和缺点

递归代码很简洁，但是不易理解，并且需要调用自身，函数调用是有时间和空间的消耗的，每一次函数调用都要在内存栈中分配空间来存储参数，返回地址和临时变量等等，而且数据向栈中的压入和弹出又会消耗时间。同时递归会产生大量重复计算，而迭代就可以借助记忆表来规避这里，其实动态规划就是这样。递归的次数太多容易造成栈溢出。

迭代代码比较繁琐，而且对于深度搜索遍历这种还需要借助栈的使用，但是比较直观，而且没有调用函数的消耗。

## ASCII码对照表

| 二进制    | 十进制 | 十六进制 | 缩写 | 可以显示的表示法 | 名称/意义                           |
| --------- | ------ | -------- | ---- | ---------------- | ----------------------------------- |
| 0000 0000 | 0      | 00       | NUL  | ␀                | 空字符（Null）                      |
| 0000 0001 | 1      | 01       | SOH  | ␁                | 标题开始                            |
| 0000 0010 | 2      | 02       | STX  | ␂                | 本文开始                            |
| 0000 0011 | 3      | 03       | ETX  | ␃                | 本文结束                            |
| 0000 0100 | 4      | 04       | EOT  | ␄                | 传输结束                            |
| 0000 0101 | 5      | 05       | ENQ  | ␅                | 请求                                |
| 0000 0110 | 6      | 06       | ACK  | ␆                | 确认回应                            |
| 0000 0111 | 7      | 07       | BEL  | ␇                | 响铃                                |
| 0000 1000 | 8      | 08       | BS   | ␈                | 退格                                |
| 0000 1001 | 9      | 09       | HT   | ␉                | 水平定位符号                        |
| 0000 1010 | 10     | 0A       | LF   | ␊                | 换行键                              |
| 0000 1011 | 11     | 0B       | VT   | ␋                | 垂直定位符号                        |
| 0000 1100 | 12     | 0C       | FF   | ␌                | 换页键                              |
| 0000 1101 | 13     | 0D       | CR   | ␍                | 归位键                              |
| 0000 1110 | 14     | 0E       | SO   | ␎                | 取消变换（Shift out）               |
| 0000 1111 | 15     | 0F       | SI   | ␏                | 启用变换（Shift in）                |
| 0001 0000 | 16     | 10       | DLE  | ␐                | 跳出数据通讯                        |
| 0001 0001 | 17     | 11       | DC1  | ␑                | 设备控制一（XON 启用软件速度控制）  |
| 0001 0010 | 18     | 12       | DC2  | ␒                | 设备控制二                          |
| 0001 0011 | 19     | 13       | DC3  | ␓                | 设备控制三（XOFF 停用软件速度控制） |
| 0001 0100 | 20     | 14       | DC4  | ␔                | 设备控制四                          |
| 0001 0101 | 21     | 15       | NAK  | ␕                | 确认失败回应                        |
| 0001 0110 | 22     | 16       | SYN  | ␖                | 同步用暂停                          |
| 0001 0111 | 23     | 17       | ETB  | ␗                | 区块传输结束                        |
| 0001 1000 | 24     | 18       | CAN  | ␘                | 取消                                |
| 0001 1001 | 25     | 19       | EM   | ␙                | 连接介质中断                        |
| 0001 1010 | 26     | 1A       | SUB  | ␚                | 替换                                |
| 0001 1011 | 27     | 1B       | ESC  | ␛                | 跳出                                |
| 0001 1100 | 28     | 1C       | FS   | ␜                | 文件分割符                          |
| 0001 1101 | 29     | 1D       | GS   | ␝                | 组群分隔符                          |
| 0001 1110 | 30     | 1E       | RS   | ␞                | 记录分隔符                          |
| 0001 1111 | 31     | 1F       | US   | ␟                | 单元分隔符                          |
| 0111 1111 | 127    | 7F       | DEL  | ␡                | 删除                                |

| 二进制    | 十进制 | 十六进制 | 图形        |
| --------- | ------ | -------- | ----------- |
| 0010 0000 | 32     | 20       | （空格）(␠) |
| 0010 0001 | 33     | 21       | !           |
| 0010 0010 | 34     | 22       | "           |
| 0010 0011 | 35     | 23       | #           |
| 0010 0100 | 36     | 24       | $           |
| 0010 0101 | 37     | 25       | %           |
| 0010 0110 | 38     | 26       | &           |
| 0010 0111 | 39     | 27       | '           |
| 0010 1000 | 40     | 28       | (           |
| 0010 1001 | 41     | 29       | )           |
| 0010 1010 | 42     | 2A       | *           |
| 0010 1011 | 43     | 2B       | +           |
| 0010 1100 | 44     | 2C       | ,           |
| 0010 1101 | 45     | 2D       | -           |
| 0010 1110 | 46     | 2E       | .           |
| 0010 1111 | 47     | 2F       | /           |
| 0011 0000 | 48     | 30       | 0           |
| 0011 0001 | 49     | 31       | 1           |
| 0011 0010 | 50     | 32       | 2           |
| 0011 0011 | 51     | 33       | 3           |
| 0011 0100 | 52     | 34       | 4           |
| 0011 0101 | 53     | 35       | 5           |
| 0011 0110 | 54     | 36       | 6           |
| 0011 0111 | 55     | 37       | 7           |
| 0011 1000 | 56     | 38       | 8           |
| 0011 1001 | 57     | 39       | 9           |
| 0011 1010 | 58     | 3A       | :           |
| 0011 1011 | 59     | 3B       | ;           |
| 0011 1100 | 60     | 3C       | <           |
| 0011 1101 | 61     | 3D       | =           |
| 0011 1110 | 62     | 3E       | >           |
| 0011 1111 | 63     | 3F       | ?           |

| 0100 0000 | 64   | 40   | @    |
| --------- | ---- | ---- | ---- |
| 0100 0001 | 65   | 41   | A    |
| 0100 0010 | 66   | 42   | B    |
| 0100 0011 | 67   | 43   | C    |
| 0100 0100 | 68   | 44   | D    |
| 0100 0101 | 69   | 45   | E    |
| 0100 0110 | 70   | 46   | F    |
| 0100 0111 | 71   | 47   | G    |
| 0100 1000 | 72   | 48   | H    |
| 0100 1001 | 73   | 49   | I    |
| 0100 1010 | 74   | 4A   | J    |
| 0100 1011 | 75   | 4B   | K    |
| 0100 1100 | 76   | 4C   | L    |
| 0100 1101 | 77   | 4D   | M    |
| 0100 1110 | 78   | 4E   | N    |
| 0100 1111 | 79   | 4F   | O    |
| 0101 0000 | 80   | 50   | P    |
| 0101 0001 | 81   | 51   | Q    |
| 0101 0010 | 82   | 52   | R    |
| 0101 0011 | 83   | 53   | S    |
| 0101 0100 | 84   | 54   | T    |
| 0101 0101 | 85   | 55   | U    |
| 0101 0110 | 86   | 56   | V    |
| 0101 0111 | 87   | 57   | W    |
| 0101 1000 | 88   | 58   | X    |
| 0101 1001 | 89   | 59   | Y    |
| 0101 1010 | 90   | 5A   | Z    |
| 0101 1011 | 91   | 5B   | [    |
| 0101 1100 | 92   | 5C   | \    |
| 0101 1101 | 93   | 5D   | ]    |
| 0101 1110 | 94   | 5E   | ^    |
| 0101 1111 | 95   | 5F   | _    |

| 二进制    | 十进制 | 十六进制 | 图形 |
| --------- | ------ | -------- | ---- |
| 0110 0000 | 96     | 60       | `    |
| 0110 0001 | 97     | 61       | a    |
| 0110 0010 | 98     | 62       | b    |
| 0110 0011 | 99     | 63       | c    |
| 0110 0100 | 100    | 64       | d    |
| 0110 0101 | 101    | 65       | e    |
| 0110 0110 | 102    | 66       | f    |
| 0110 0111 | 103    | 67       | g    |
| 0110 1000 | 104    | 68       | h    |
| 0110 1001 | 105    | 69       | i    |
| 0110 1010 | 106    | 6A       | j    |
| 0110 1011 | 107    | 6B       | k    |
| 0110 1100 | 108    | 6C       | l    |
| 0110 1101 | 109    | 6D       | m    |
| 0110 1110 | 110    | 6E       | n    |
| 0110 1111 | 111    | 6F       | o    |
| 0111 0000 | 112    | 70       | p    |
| 0111 0001 | 113    | 71       | q    |
| 0111 0010 | 114    | 72       | r    |
| 0111 0011 | 115    | 73       | s    |
| 0111 0100 | 116    | 74       | t    |
| 0111 0101 | 117    | 75       | u    |
| 0111 0110 | 118    | 76       | v    |
| 0111 0111 | 119    | 77       | w    |
| 0111 1000 | 120    | 78       | x    |
| 0111 1001 | 121    | 79       | y    |
| 0111 1010 | 122    | 7A       | z    |
| 0111 1011 | 123    | 7B       | {    |
| 0111 1100 | 124    | 7C       | \|   |
| 0111 1101 | 125    | 7D       | }    |
| 0111 1110 | 126    | 7E       | ~    |



## 环形链表的解释

有一个对环形链表的解释非常清晰

![image-20210113224933362](C:\Users\DLSH\AppData\Roaming\Typora\typora-user-images\image-20210113224933362.png)

![image-20210113225047171](C:\Users\DLSH\AppData\Roaming\Typora\typora-user-images\image-20210113225047171.png)

![image-20210113225107962](C:\Users\DLSH\AppData\Roaming\Typora\typora-user-images\image-20210113225107962.png)

## Vector 删除元素

| 函数                  | 功能                                                         |
| --------------------- | ------------------------------------------------------------ |
| pop_back()            | 删除 vector 容器中最后一个元素，该容器的大小（size）会减 1，但容量（capacity）不会发生改变。 |
| erase(pos)            | 删除 vector 容器中 pos 迭代器指定位置处的元素，并返回指向被删除元素下一个位置元素的迭代器。该容器的大小（size）会减 1，但容量（capacity）不会发生改变。 |
| swap(beg)、pop_back() | 先调用 swap() 函数交换要删除的目标元素和容器最后一个元素的位置，然后使用 pop_back() 删除该目标元素。 |
| erase(beg,end)        | 删除 vector 容器中位于迭代器 [beg,end)指定区域内的所有元素，并返回指向被删除区域下一个位置元素的迭代器。该容器的大小（size）会减小，但容量（capacity）不会发生改变。 |
| remove()              | 删除容器中所有和指定元素值相等的元素，并返回指向最后一个元素下一个位置的迭代器。值得一提的是，调用该函数不会改变容器的大小和容量。 |
| clear()               | 删除 vector 容器中所有的元素，使其变成空的 vector 容器。该函数会改变 vector 的大小（变为 0），但不是改变其容量。 |

## C++输入输出流对字符串的应用

输入输出流在某些情况下很必要，而且非常方便和快速

首先有三种输入输出流

1.基于控制台

```c++
#include <iostream> 
```

这就是打印或者接受控制台的交互信息

istream 读取流

ostream 写入流

iostream 对流读写

2.基于文件

```c++
#include <fstream>
```

写入文件，保存数据或者读取文件

ifstream 读取文件

ostream 写入文件

fstream 对文件读写

3.基于字符串，在算法题中使用得当的话很方便，而且主要是很快

```c++
#include <sstream>
```

istringstream 从string读

ostringstream 写入string

stringstream 读写string

1. istringstream类

   描述：从流中提取数据，支持 >> 操作

   这里字符串可以包括多个单词，单词之间使用**空格**分开,多个单词都是**string**

   1. istringstream的构造函数原形： 
   2. istringstream::istringstream(string str); 

   初始化：使用**字符串**进行初始化

   1. istringstream istr("1 56.7"); 
   2. istr.str("1 56.7");//把字符串"1 56.7"存入字符串流中 

**str()：使istringstream对象返回一个string字符串**

```c++
1. istringstream istr("1 56.7"); 
2.  
3.   cout<<istr.str()<<endl;//直接输出字符串的数据 "1 56.7"  
4.    
5.   string str = istr.str();//函数str()返回一个字符串  
6.   cout<<str<<endl; 
7.    
8.   int n; 
9.   double d; 
10.  
11.   //以空格为界，把istringstream中数据取出，应进行类型转换  
12.   istr>>n;//第一个数为整型数据，输出1  
13.   istr>>d;//第二个数位浮点数，输出56.7  
14.  
15.   //假设换下存储类型  
16.   istr>>d;//istringstream第一个数要自动变成浮点型，输出仍为1  
17.   istr>>n;//istringstream第二个数要自动变成整型，有数字的阶段，输出为56 
```

用流来转格式非常方便

还有一种功能,**把一行字符串放入流中，单词以空格隔开。之后把一个个单词从流中依次读取到字符串**

```c++
istringstream istr;  
    string line,str;  
    while (getline(cin,line))//从终端接收一行字符串，并放入字符串line中   
    {  
        istr.str(line);//把line中的字符串存入字符串流中   
        while(istr >> str)//每次读取一个单词（以空格为界），存入str中   
        {  
            cout<<str<<endl;  
        }  
    }  
```

每次读取一个stirng，最后有终止符\0就自动跳出了

## unordered_map 哈希表

今天做题遇到了哈希表，有点忘了，写了个w.second()一直错，才发现pair的第一个和第二个是没有括号的，顺便把stl哈希表的一些信息记一下

unordered_map和map类似，都是存储的key-value的值，可以通过key快速索引到value。不同的是**unordered_map不会根据key的大小进行排序**，存储时是根据key的hash值判断元素是否相同，即unordered_map内部元素是无序的。unordered_map的底层是一个防冗余的哈希表（开链法避免地址冲突）。unordered_map的key需要定义hash_value函数并且重载operator ==。

哈希表最大的优点，就是把数据的存储和查找消耗的时间大大降低，时间复杂度为O(1)；而代价仅仅是消耗比较多的内存。哈希表的查询时间虽然是O(1)，但是并不是unordered_map查询时间一定比map短，因为实际情况中还要考虑到数据量，而且unordered_map的hash函数的构造速度也没那么快，所以不能一概而论，应该具体情况具体分析。

```c++
#include<string>  
#include<iostream>  
#include<unordered_map>
using namespace std;  
  
int main()
{
	unordered_map<string, int>  dict; // 声明unordered_map对象
	
	// 插入数据的三种方式
	dict.insert(pair<string,int>("apple",2));
	dict.insert(unordered_map<string, int>::value_type("orange",3));
	dict["banana"] = 6;
	
	// 判断是否有元素
	if(dict.empty())
		cout<<"该字典无元素"<<endl;
	else
		cout<<"该字典共有"<<dict.size()<<"个元素"<<endl;
	
	// 遍历
	unordered_map<string, int>::iterator iter;
	for(iter=dict.begin();iter!=dict.end();iter++)
		cout<<iter->first<<ends<<iter->second<<endl;
	
	// 查找
	if(dict.count("boluo")==0)
		cout<<"can't find boluo!"<<endl;
	else
		cout<<"find boluo!"<<endl;
	
	if((iter=dict.find("banana"))!=dict.end())
		cout<<"banana="<<iter->second<<endl;
	else
		cout<<"can't find boluo!"<<endl;
	
	return 0;
}
```

## C++ unordered_set容器的成员方法

unordered_set 类模板中，提供了如表 2 所示的成员方法。

| 成员方法           | 功能                                                         |
| ------------------ | ------------------------------------------------------------ |
| begin()            | 返回指向容器中第一个元素的正向迭代器。                       |
| end();             | 返回指向容器中最后一个元素之后位置的正向迭代器。             |
| cbegin()           | 和 begin() 功能相同，只不过其返回的是 const 类型的正向迭代器。 |
| cend()             | 和 end() 功能相同，只不过其返回的是 const 类型的正向迭代器。 |
| empty()            | 若容器为空，则返回 true；否则 false。                        |
| size()             | 返回当前容器中存有元素的个数。                               |
| max_size()         | 返回容器所能容纳元素的最大个数，不同的操作系统，其返回值亦不相同。 |
| **find(key)**      | 查找以值为 key 的元素，**如果找到，则返回一个指向该元素的正向迭代器**；反之，**则返回一个指向容器中最后一个元素之后位置的迭代器（如果 end() 方法返回的迭代器）。** |
| **count(key)**     | **在容器中查找值为 key 的元素的个数。**                      |
| equal_range(key)   | 返回一个 pair 对象，其包含 2 个迭代器，用于表明当前容器中值为 key 的元素所在的范围。 |
| **emplace()**      | **向容器中添加新元素，效率比 insert() 方法高。**             |
| **emplace_hint()** | **向容器中添加新元素，效率比 insert() 方法高。**             |
| **insert()**       | **向容器中添加新元素。**                                     |
| erase()            | 删除指定元素。                                               |
| **clear()**        | **清空容器，即删除容器中存储的所有元素。**                   |
| swap()             | 交换 2 个 unordered_map 容器存储的元素，前提是必须保证这 2 个容器的类型完全相等。 |
| bucket_count()     | 返回当前容器底层存储元素时，使用桶（一个线性链表代表一个桶）的数量。 |
| max_bucket_count() | 返回当前系统中，unordered_map 容器底层最多可以使用多少桶。   |
| bucket_size(n)     | 返回第 n 个桶中存储元素的数量。                              |
| bucket(key)        | 返回值为 key 的元素所在桶的编号。                            |
| load_factor()      | 返回 unordered_map 容器中当前的负载因子。负载因子，指的是的当前容器中存储元素的数量（size()）和使用桶数（bucket_count()）的比值，即 load_factor() = size() / bucket_count()。 |
| max_load_factor()  | 返回或者设置当前 unordered_map 容器的负载因子。              |
| rehash(n)          | 将当前容器底层使用桶的数量设置为 n。                         |
| reserve()          | 将存储桶的数量（也就是 bucket_count() 方法的返回值）设置为至少容纳count个元（不超过最大负载因子）所需的数量，并重新整理容器。 |
| hash_function()    | 返回当前容器使用的哈希函数对象。                             |


注意，此容器模板类中没有重载 [ ] 运算符，也没有提供 at() 成员方法。不仅如此，**由于 unordered_set 容器内部存储的元素值不能被修改**，**因此无论使用那个迭代器方法获得的迭代器，都不能用于修改容器中元素的值。**

## switch

switch 的case后面必须是常量！！！！

因为C语言中的 switch 不是 if 的替代品。

编译时会对 switch 进行优化，根据 case 标签后面的常量值，生成跳转表，只经过少数次数的比较，就可以跳到对应标签下面。所以，标签也是不能重复的。如果允许变量，switch 只能退化成跟一连串的 if else, 对于一个数据也只能从头到尾地进行比较，也就失去了 switch 的意义。跳转表和逐个比较，这两种方式的复杂度差很多

## 查找

**lower_bound 第一个>=的元素**

**upper_bound 第一个>的元素**

lower_bound()返回bai一个 iterator 它指向在[first,last)标记的du有序序列中可以插入zhivalue，而不会破坏dao器顺序zhuan的第一个位置，shu而这个位置标记了一个不小于value 的值。
例如，有如下序列：
ia[]={12,15,17,19,20,22,23,26,29,35,40,51};
用值21调用lower_bound(),返回一个指向22的iterator。用值22调用lower_bound(),也返回一个指向22的iterator。第一个版本使用底层的 < (小于)操作符，第二个版本根据comp进行排序和比较。
注意事项
调用lower_bound之前必须确定序列为**有序序列**，否则调用出错。第一个版本排序根据底层的 <(小于)操作符，第二个版本根据comp进行排序。

## unsigned 和signed

**1.unsigned 类型转换为 signed类型的时候是直接复制到低位,高位为0.如果signed类型位数不够，只直接装载unsigned低位。**

 **2.signed类型转换为unsigned类型的时候，也是将补码直接复制到低位,高位为符号位。如果unsigned位数不够，只直接装载signed低位。**

**补码直接复制！！!!!!!!!!!**