URL

<https://grpc.io/docs/guides/concepts/>



Bidirectional streaming RPCs where both sides send a sequence of messages using a read-write stream. The two streams operate independently, so clients and servers can read and write in whatever order they like: for example, the server could wait to receive all the client messages before writing its responses, or it could alternately read a message then write a message, or some other combination of reads and writes. The order of messages in each stream is preserved.

双向流式RPC，双方使用读写流发送一系列消息。 这两个流独立运行，因此客户端和服务器可以按照自己喜欢的顺序进行读写：例如，服务器可以在写入响应之前等待接收所有客户端消息，或者它可以交替地读取消息然后写入消息， 或者其他一些读写组合。 保留每个流中的消息顺序。



Synchronous vs. asynchronous

Synchronous RPC calls that block until a response arrives from the server are the closest approximation to the abstraction of a procedure call that RPC aspires to. On the other hand, networks are inherently asynchronous and in many scenarios it’s useful to be able to start RPCs without blocking the current thread.

同步与异步

在响应从服务器到达之前阻塞的同步RPC调用最接近RPC所期望的过程调用的抽象。 另一方面，网络本质上是异步的，在许多情况下，能够在不阻塞当前线程的情况下启动RPC非常有用。



