# JAVA NIO

# 1 JAVA IO

基本概念

![javaIO](javaNio\javaIO.jpg)

# 2 JAVA NIO

​        Java NIO（New IO）是从Java 1.4版本开始引入的一个新的IO API，可以替代标准的Java IO API。NIO与原来的IO有同样的作用和目的，但是使用的方式完全不同，NIO支持面向缓冲区的、基于通道的IO操作。NIO将以更加高效的方式进行文件的读写操作。

##  2.1 Java NIO的新特性为：

![](javaNio\javaNio新特性.png)

## 2.2 核心组件

Java NIO的核心组件为：

* 通道（Channel）
* 缓冲区（Buffer）
* 选择器（Selector）

![javanio核心组件](javaNio\java核心组件.png)

​        Java NIO系统的核心在于：通道(Channel)和缓冲区(Buffer)。通道表示打开到 IO 设备(例如：文件、套接字)的连接。若需要使用 NIO 系统，需要获取用于连接 IO 设备的通道以及用于容纳数据的缓冲区。然后操作缓冲区，对数据进行处理。即通道负责传输，缓存负责存储。

## 2.3 缓冲区（Buffer）

​         一个用于特定**基本数据类型**的容器。由 java.nio 包定义的，所有缓冲区都是 Buffer 抽象类的子类。 Java NIO 中的 Buffer 主要用于与 NIO 通道进行交互，数据是从通道读入缓冲区，从缓冲区写入通道中的。

### 2.3.1 缓冲区常用的子类

​       Buffer 就像一个数组，可以保存多个相同类型的数据。根据数据类型不同(boolean 除外) ，有以下 Buffer 常用子类：

* ByteBuffer  字节缓存
* CharBuffer  字符缓存
*  ShortBuffer 短整型
*  IntBuffer    整型
* LongBuffer 长整型
* FloatBuffer 单精度
* DoubleBuffer 双精度

​        上述 Buffer 类 他们都采用相似的方法进行管理数据，只是各自管理的数据类型不同而已。都是通过如下方法获取一个 Buffer对象：
​        **static XxxBuffer allocate(int capacity) : 创建一个容量为capacity 的 XxxBuffer 对象**

### 2.3.2 缓冲区的基本属性

* **容量 (capacity)** ：表示 Buffer 最大数据容量，缓冲区容量不能为负，并且创建后不能更改。

* **限制 (limit)** **：**第一个不应该读取或写入的数据的索引，即位于 limit 后的数据不可读写。缓冲区的限制不能为负，并且不能大于其容量。

* **位置 (position)：**下一个要读取或写入的数据的索引。缓冲区的位置不能为
  负，并且不能大于其限制；

*  **标记 (mark) 与重置 (reset) ：**标记是一个索引，通过 Buffer 中的 mark() 方法指定 Buffer 中一个特定的 position，之后可以通过调用 reset() 方法恢复到这个 position.

   标记 、 位置 、 限制 、 容量遵守以下不变式： 0 <= mark <= position <= limit <= capacity

### 2.3.3 Buffer的数据操作

Buffer 所有子类提供了两个用于数据操作的方法：get()与 put() 方法

* 获取 Buffer  中的数据（读）
  * get() ：读取单个字节
  * get(byte[] dst)：批量读取多个字节到 dst 中
  * get(int index)：读取指定索引位置的字节(不会移动 position)
* 放入数据到 Buffer 中（写）
  * put(byte b)：将给定单个字节写入缓冲区的当前位置
  * put(byte[] src)：将 src 中的字节写入缓冲区的当前位置
  * put(int index, byte b)：将指定字节写入缓冲区的索引位置(不会移动 position)

### 2.3.4 直接与非直接字节缓冲区

* 字节缓冲区要么是直接的，要么是非直接的。如果为直接字节缓冲区，则 Java  虚拟机会尽最大努力直接在
  此缓冲区上执行本机 I/O  操作。也就是说，在每次调用基础操作系统的一个本机 I/O  操作之前（或之后），虚拟机都会尽量避免将缓冲区的内容复制到中间缓冲区中（或从中间缓冲区中复制内容）。
* 直接字节缓冲区可以通过调用此类的 allocateDirect()工厂方法 来创建。此方法返回的缓冲区进行分配和取消
  分配所需成本通常高于非直接缓冲区 。直接缓冲区的内容可以驻留在常规的垃圾回收堆之外，因此，它们对
  应用程序的内存需求量造成的影响可能并不明显。所以，建议将直接缓冲区主要分配给那些易受基础系统的
  本机 I/O 操作影响的大型、持久的缓冲区。一般情况下，最好仅在直接缓冲区能在程序性能方面带来明显好处时分配它们。
* 直接字节缓冲区还可以通过FileChannel的map()方法将文件区域直接映射到内存中来创建 。该方法返回MappedByteBuffer。Java 平台的实现有助于通过 JNI从本机代码创建直接字节缓冲区。如果以上这些缓冲区中的某个缓冲区实例指的是不可访问的内存区域，则试图访问该区域不会更改该缓冲区的内容，并且将会在访问期间或稍后的某个时间导致抛出不确定的异常。
* 字节缓冲区是直接缓冲区还是非直接缓冲区可通过调用其 isDirect()  方法来确定。提供此方法是为了能够在
  性能关键型代码中执行显式缓冲区管理 。

## 2.4 通道（channel）

​       通道（Channel）：由 java.nio.channels 包定义的。Channel 表示 IO 源与目标打开的连接。Channel 类似于传统的“流”。只不过 Channel本身不能直接访问数据，Channel 只能与Buffer 进行交互。

### 2.4.1 Java 为 为  Channel  接口提供的最主要实现类

* FileChannel：用于读取、写入、映射和操作文件的通道。
* DatagramChannel：通过 UDP 读写网络中的数据通道。
* SocketChannel：通过 TCP 读写网络中的数据。
* ServerSocketChannel：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个SocketChannel。

### 2.4.2 获取通道

获取通道的一种方式是对支持通道的对象调用getChannel() 方法。支持通道的类如下：

* FileInputStream
* FileOutputStream
* RandomAccessFile
* DatagramSocket
* Socket
* ServerSocket

### 2.4.3 通道的数据传输

* 将缓存中的数据写到通道中

  int bytesWritten = inChannel.write(buf);

* 从Channel读取数据到缓存

  int bytesRead = inChannel.read(buf);

* 分散和聚集

  * 分散

    分散读取（Scattering Reads）是指从 Channel 中读取的数据“分散”到多个 Buffer 中；

    按照缓冲区的顺序，从 Channel 中读取的数据依次将 Buffer 填满。

  * 聚集

    聚集写入（Gathering Writes）是指将多个 Buffer 中的数据“聚集”到 Channel；

    注意：按照缓冲区的顺序，写入 position 和 limit 之间的数据到 Channel 。

* 通道之间的数据传输api

  * transferFrom()
  * transferTo()

