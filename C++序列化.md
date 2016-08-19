# C-tips

导读test

1. 什么是序列化？

2. 为什么要序列化？好处在哪里？

3. C++对象序列化的四种方法

4. 最常用的两种序列化方案使用心得


正文

1. 什么是序列化？
程序员在编写应用程序的时候往往需要将程序的某些数据存储在内存中，然后将其写入某个文件或是将它传输到网络中的另一台计算机上以实现通讯。这个将 程序数据转化成能被存储并传输的格式的过程被称为“序列化”（Serialization），而它的逆过程则可被称为“反序列化” （Deserialization）。

简单来说，序列化就是将对象实例的状态转换为可保持或传输的格式的过程。与序列化相对的是反序列化，它根据流重构对象。这两个过程结合起来，可以轻 松地存储和传输数据。例如，可以序列化一个对象，然后使用 HTTP 通过 Internet 在客户端和服务器之间传输该对象。

 

总结

序列化：将对象变成字节流的形式传出去。

反序列化：从字节流恢复成原来的对象。

2. 为什么要序列化？好处在哪里？
简单来说，对象序列化通常用于两个目的：  
（1） 将对象存储于硬盘上  ，便于以后反序列化使用
（2）在网络上传送对象的字节序列


对象序列化的好处在哪里？网络传输方面的便捷性、灵活性就不说了，这里举个我们经常可能发生的需求：你 有一个数据结构，里面存储的数据是经过很多其它数据通过非常复杂的算法生成的，由于数据量很大，算法又复杂，因此生成该数据结构所用数据的时间可能要很久 （也许几个小时，甚至几天），生成该数据结构后又要用作其它的计算，那么你在调试阶段，每次运行个程序，就光生成数据结构就要花上这么长的时间，无疑代价 是非常大的。如果你确定生成数据结构的算法不会变或不常变，那么就可以通过序列化技术生成数据结构数据存储到磁盘上，下次重新运行程序时只需要从磁盘上读 取该对象数据即可，所花费时间也就读一个文件的时间，可想而知是多么的快，节省了我们的开发时间。

3. C++对象序列化的四种方法
将C++对象进行序列化的方法一般有四种，下面分别介绍：


3.1 Google Protocol Buffers（protobuf）
Google Protocol Buffers (GPB)是Google内部使用的数据编码方式，旨在用来代替XML进行数据交换。可用于数据序列化与反序列化。主要特性有：

高效
语言中立(Cpp, Java, Python)
可扩展
官方文档

 

3.2 Boost.Serialization
Boost.Serialization可以创建或重建程序中的等效结构，并保存为二进制数据、文本数据、XML或者有用户自定义的其他文件。该库具有以下吸引人的特性：

代码可移植（实现仅依赖于ANSI C++）。
深度指针保存与恢复。
可以序列化STL容器和其他常用模版库。
数据可移植。
非入侵性。
 

3.3 MFC Serialization
Windows平台下可使用MFC中的序列化方法。MFC 对 CObject 类中的序列化提供内置支持。因此，所有从 CObject 派生的类都可利用 CObject 的序列化协议。

MSDN中的介绍

 

3.4 .Net Framework
.NET的运行时环境用来支持用户定义类型的流化的机制。它在此过程中，先将对象的公共字段和私有字段以及类的名称（包括类所在的程序集）转换为字节流，然后再把字节流写入数据流。在随后对对象进行反序列化时，将创建出与原对象完全相同的副本。


3.5 简单总结
这几种序列化方案各有优缺点，各有自己的适用场景。其中MFC和.Net框架的方法适用范围很窄，只适用于Windows下，且.Net框架方法还需要.Net的运行环境。参考文献1从序列化时间、反序列化时间和产生数据文件大小这几个方面比较了前三种序列化方案，得出结论如下（仅供参考）：

Google Protocol Buffers效率较高，但是数据对象必须预先定义，并使用protoc编译，适合要求效率，允许自定义类型的内部场合使用。
Boost.Serialization 使用灵活简单，而且支持标准C++容器。
相比而言，MFC的效率较低，但是结合MSVS平台使用最为方便。
为了考虑平台的移植性、适用性和高效性，推荐大家使用Google的protobuf和Boost的序列化方案，下面介绍我使用这两种方案的心得及注意事项。

4. 最常用的两种序列化方案使用心得
关于这两种方案的具体使用和示例没什么好写的，因为优秀的参考资料很多，请看后面给出的相关参考资料，这里只给出我使用时的一些心得，方便大家在选择序列化方案时有个正确的参考，避免选择错误，浪费时间。


4.1 Google Protocol Buffers
protobuf相对而言效率应该是最高的，不管是安装效率还是使用效率，protobuf都很高效，而且protobuf不仅用于C++序列化，还可用于Java和Python的序列化，使用范围很广。但在使用过程中要注意两个问题：


（1）protobuf支持的数据类型不是很丰富

protobuf属于轻量级的，因此不能支持太多的数据类型，下面是protobuf支持的基本类型列表，一般都能满足需求，不过在选择方案之前，还是先看看是否都能支持，以免前功尽弃。同样该表也值得收藏，作为我们在定义类型时做参考。

.proto type

c++

notes

double

double

 

float

float

 

int32

int32

使用可变长编码方式，负数时不够高效，应该使用sint32

int64

int64

同上

uint32

uint32

使用可变长编码方式

uint64

uint64

同上

sint32

int32

使用可变长编码方式，有符号的整型值，编码时比通常的int32高效

sint64

sint64

同上

fixed32

uint32

总是4个字节，如果数值总是比2^28大的话，这个类型会比uint32高效

fixed64

uint64

总是8个字节，如果数值总是比2^56大的话，这个类型会比uint64高效

sfixed32

int32

总是4个字节

sfixed64

int64

总是8个字节

bool

bool

 

string

string

一个字符串必须是utf-8编码或者7-bit的ascii编码的文本

bytes

string

可能包含任意顺序的字节数据

 

（2）protobuf不支持二维数组（指针），不支持STL容器序列化

这个缺陷挺大，因为稍复杂点的数据结构或类结构里出现二维数组、二维指针和STL容器（set、list、map等）很频繁，但因为 protobuf简单的实现机制，只支持一维数组和指针（用repeated修饰符修饰），不能使用repeated repeated来支持二维数组， 也不支持STL，因此在选择该方案之前，一定 要确保你的数据结构里没有这些不支持的类型。


（3）protobuf嵌套后会改变类名称
protobuf支持类的嵌套，即在一个自定义类型中可以定义另一个自定义类型，但注意嵌套的自定义类型在经过protobuf处理后生成的类名称并不是你定义的类名称，而是加上了外层的类名称作为前缀，下面举一个简单的例子：

复制代码
    message DFA {  
        required int32 _size = 1;  
       
        message accept_pair {  
          required bool is_accept_state = 1;  
          required bool is_strict_end = 2;  
          optional string app_name = 3;  
        }  
       
        repeated accept_pair accept_states = 2;  
    }  
复制代码
 

那么嵌套中的accept_pair 生成后的类不是accept_pair 而是DFA_accept_pair 。如果不想改类名称，将accept_pair 拿到外面与DFA平行定义即可。


4.2 Boost.Serialization
Boost库是个很庞大的库，功能非常丰富，序列化只是其中的一个小分支，但为了使用Boost的序列化方案，你需要安装整个Boost库，所花费的磁盘空间和时间都很多，同样支持的序列化功能也很强大，既支持二维数组（指针），也支持STL容器，更不需要我们用某种特殊的格式重新定义我们的类结构，其非侵入的性质使得我们无须改动已有的类结构即可序列化，这时非常赞的一个性质。但是由于体积庞大，安装复杂，如果只是简单的序列化，没必要使用该方案，只有protobuf不能满足你的需求时，才应该考虑该方案。


（1）安装boost库遇到的一系列问题

安装boost库本事就是一项很费时的工程，如果期间出现了各种错误，更加耗时耗耐心。我们可以从官网下载Boost库的二进制源码进行安装，安装方法可以参考网络或后面我给出的参考资料。

安装过程如下：

首先解压安装包，如果是tar.gz用tar zxvf解压，如果是tar.bz2用tar jxvf解压，解压后进入解压后的目录，依次运行以下命令：

./bootstrap.sh
sudo ./b2 install
注：这里没有指定安装路径，在第二个命令可以加入--prefix指定安装目录。

 

安装时的注意事项：

注意1：要用root权限进行安装，否则会在安装过程中报错，提示权限不足。

注意2：boost库的安装依赖一些环境，通常有Python、bzip2和zlib，它们所在的软件包分别为：

Ubuntu下：

zlib1g-dev 
libbz2-dev 
libpython2.7-dev (and libpython3.3-dev)
 

Fedora/Redhat下：

zlib-devel 
libbz2-devel 
python-devel (and python3-devel) 

这也是安装过程中报错的主要来源。
报错1：如果Python库不完整，可能会报“ fatal error: pyconfig.h: No such file or directory compilation terminated.”或者“fatal error: patchlevel.h: No such file or directory”错误。解决方法如下：
Fedora系统：sudo yum install python-devel

Ubuntu系统：sudo apt-get  install python-dev


报错2：报错 “ libs/iostreams/src/bzip2.cpp:20:56: fatal error: bzlib.h: No such file or directory”，解决方案：

Fedora系统：sudo yum install bzip2-devel

Ubuntu系统或Debian系统：sudo apt-get install libbz2-dev

通常对于这些错误，在Ubuntu系统下一般可以通过sudo apt-get install libboost-all-dev全部解决，但不一定行得通。

（2）安装成功后，如果未指定安装位置，那么默认将会安装到/usr/local/lib和/usr/local/include下，那么我们在使用Boost库进行编译时就需要使用-L和-I参数加上具体的lib和include路径，像下面这样：

g++ -o test boost_test.cpp -I$BOOST_INCLUDE -L$BOOST_LIB -lboost_serialization
如果觉得每次都这样很麻烦，那么可以将我们所要用到的lib和include文件加入到环境变量中，像下面这样：

sudo cp /usr/local/lib/libboost_serialization.* /usr/lib
sudo cp -r /usr/local/include/boost /usr/include
然后在编译时直接g++ -o test boost_test.cpp -lboost_serialization即可。

注意：boost下面有两个序列化lib文件：ibboost_serialization.lib 和 libboost_wserialization.lib，那么这两者有什么区别呢？

其实'w' 表示使用的是宽字符，例如 wchar_t。


（3）boost不尽人意的地方

基本类型指针很难序列化，例如int *array，官网上是这么说的：
By default, data types designated primitive by Implementation Levelclass serialization trait are never tracked. If it is desired totrack a shared primitive object through a pointer (e.g. along used as a reference count), It should be wrappedin a class/struct so that it is an identifiable type.The alternative of changing the implementation level of alongwould affect alllongs serialized in the wholeprogram - probably not what one would intend.
也就是说如果你想序列化原生类型的指针，需要给其加上struct或class使其变为类类型再序列化，可见有些麻烦，这样的需求往往也很频繁，鉴于序列化机制的实现原理，boost库暂时还不能很好的支持基本类型的指针序列化。
不能序列化变长数组（variable-sized array），会报错说变长数组不是模板类类型。

（4）如果需要定义一个对象数组，如定义含有2个元素的class A对象数组，那么必须用A a[2]定义而不能用对象的指针A *a = new A[2]定义，这样序列化a后默认当作一个A对象处理，因此只能存储一个对象的值，后面的不会存储。


（5）所谓boost很人性的非侵入性质也有一定的条件：如果不想改动原来的类，那么原来的类属性必须是public的，这很容易解释，因为你必须 要能在别处访问到这些属性并定义其序列化方式，当然这也在其它地方暴露了类的结构，具有一定的劣势。这样的条件往往很难满足，因为我们定义的类属性一般都 是private的，如果是这样，且仍想要使用非侵入性质，那么需要在类中添加以下声明来开放访问给 serialization 库：

friend class boost::serialization::access; 
这样的方式比让成员public更好。
