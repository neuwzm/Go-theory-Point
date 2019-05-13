URL

<https://grpc.io/docs/guides/concepts/>



Bidirectional streaming RPCs where both sides send a sequence of messages using a read-write stream. The two streams operate independently, so clients and servers can read and write in whatever order they like: for example, the server could wait to receive all the client messages before writing its responses, or it could alternately read a message then write a message, or some other combination of reads and writes. The order of messages in each stream is preserved.

双向流式RPC，双方使用读写流发送一系列消息。 这两个流独立运行，因此客户端和服务器可以按照自己喜欢的顺序进行读写：例如，服务器可以在写入响应之前等待接收所有客户端消息，或者它可以交替地读取消息然后写入消息， 或者其他一些读写组合。 保留每个流中的消息顺序。



