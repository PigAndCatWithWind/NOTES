# NIO

## 缓冲区

### 一、缓冲区（Buffer）：在java NIO 中负责数据的存取，缓冲区就是数组，用于存储不同数据类型的数据。

根据数据类型不同（boolean除外），提供了相应类型的缓冲区：

ByteBuffer

CharBuffer

ShortBuffer

IntBuffer

LongBuffer

FloatBuffer

DoubleBuffer

上述缓冲区的管理方式几乎一致，通过 allocate() 获取缓冲区

### 二、缓冲区出存储数据的两个和行方法：

put()：存入数据到缓冲区中;

get()：获取缓冲区的数据。

### 三、缓冲区中的四个核心属性：

capacity：容量，表示缓冲区中最大的容量;

limit：界限，表示缓冲区中可以操作数据的大小（limit后面的数据不可以进行读写）;

position：位置，表示缓冲区正在进行操作的数据位置;

mark：标记，表示记录当前position的位置。可以通过reset()回复到mark的位置。

### 四、直接缓冲区与非直接缓冲区：

非直接缓冲区：通过allocate()方法分配缓冲区，将缓冲区直接建立在JVM 内存中;

直接缓冲区：通过allocateDirect()方法分配直接缓冲区，将缓冲区建立在物理内存中，可以提高效率。

## 通道

### 一、 通道（Channel）：用于源节点与目标节点的连接。在java NIO 中负责缓冲区中数据的传输。Channel本身不存储数据，因此需要配合缓冲区进行传输。

### 二、通道的主要实现类

java.nio.channels.Channel 接口：

---> FileChannel

--->SocketChannel

--->ServerSocketChannel

--->DatagramChannel

###  三、获取通道

1. Java针对支持通道提供了getChannel()方法

   本地：

   FileInputStream/FileOutoutStream

   RandomAccessFile

   网络IO ：

   Socket

   ServerSocket

   DatagramSocket

2. 在JDK 1.7中NIO 2针对各个通道提供了静态方法open()
3. 在JDK1.7 中NIO 2的Files工具类的newByteChannel()

### 四、通道之间的数据传输

transferFrom();

transferTo();

### 五、分散（Scatter）与聚集（Gather）

分散读取（scattering reads）：将通道中的数据分散到多个缓冲区中;

聚集写入（gathering writes）：将多个缓冲区中的数据聚集到通道中。

### 六、字符集

编码：字符串---> 字符数组

解码：字符数组---> 字符串

## 使用NIO 完成网络通信的三个核心

### 一、通道（Channel）：负责链接：

java.nio.channels.Channel 接口：

​	|--SelectableChannel

​		|--Socketchannel

​		|--ServerSocketChannel

​		|--DatagramChannel



​		|--Pipe.SinkChannel

​		|--Pipe.SourceChannel

### 二、缓冲区（Buffer）：负责数据的存取

### 三、选择器（Selector）：是SelectableChannel的多路复用器

