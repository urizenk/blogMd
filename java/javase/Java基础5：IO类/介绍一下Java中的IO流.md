解答方向：IO流的分类+装饰器

Java中的IO用于实现对数据的输入输出进行操作，Java把不同的输入输出源（文件，外设，网络传输）抽象为流，流是从起源到接收的有序的数据。

IO的分类：

按照数据的流向：从外部流向主机的称为输入流，可以读取数据，从主机流向外部的称为输出流，可以写入数据，针对不同的传输格式有不同的命名（字节的称为inputStream和outputStream，字符的称为reader和writer）

按照数据的分类：操作字节的称为字节流，操作8位的字节，操作字符的称为字符流，操作16位的字符。

按照处理的功能来分：负责连接一个特定的IO设备读写数据，也被称为低级流，而处理流负责连接或者封装节点流，用于简化数据读写和提高效率，也被称为高级流。

Java中的节点流有：

可以操作file，内存数组和内存中的字符串以及管道。

![](E:\文档\博文\java\javase\Java基础5：IO类\asset\Snipaste_2022-11-30_18-19-14.png)

Java中的处理流有：

1.缓冲流

以Buffered开头的流，内部开辟一个缓冲区以加快数据的存储速度。

2.转换流

都是文字流，一个InputStreamReader将字节流转换为字符流，一个OutputStreamWriter将字符流转换为字节流。

3.标准输入输出流

System.in和System.out分别代表了系统标准的输入与输出设备，默认的输出设备是显示器，默认的输入设备是键盘，System.in的类型是InputStream，System.out的类型为PrintStream，是OutputStream的子类。重定向功能通过System的setIn（）和setOut方法对默认设备进行改变。

4.打印流

将基本数据类型的数据格式化转换为字符串输出。

5.数据流

为了方便的操作Java语言的基本类型和String类

6.对象流

ObjectInputStream和ObjectOutputStream用于存取和读取基本类型数据或者对象的处理流，他们不能序列化static和transient修饰的成员变量

