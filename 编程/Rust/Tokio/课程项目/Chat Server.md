> 课程：Async `Rust（软件工艺师）

# 步骤

## 安装Tokio

在Cargo.toml中添加
```
tokio = { version = "1", features = ["full"] }
```

或者直接命令行（安装最新版本，features为full）
```
cargo add tokio -F full
```
之后Cargo.toml中显示
```
tokio = { version = "1.48.0", features = ["full"] }
```

## Echo Server
> 实现回传服务器

使用Tokio异步运行时构建一个简单的TCP服务器（Echo Server）。这里的作用是监听`localhost:8080`（本地回环IP地址的8080端口），接收客户端连接，并将客户端每行输出的消息按照原样回显回去。

### 代码部分

``` rust
use tokio::{
    io::{AsyncBufReadExt, AsyncWriteExt, BufReader},
    net::TcpListener,
};

#[tokio::main]
async fn main() {
    let lisntener = TcpListener::bind("localhost:8080").await.unwrap();
    loop {
        let (mut socket, addr) = lisntener.accept().await.unwrap();
        println!("{:#?}", socket);
        tokio::spawn(async move {
            let (stream_reader, mut stream_writer) = socket.split();
            let mut message = String::new();
            let mut reader = BufReader::new(stream_reader);
            loop {
                let bytes_read = reader.read_line(&mut message).await.unwrap();
                if bytes_read == 0 {
                    break;
                }
                stream_writer.write_all(message.as_bytes()).await.unwrap();
                println!("size: {bytes_read}");
                println!("message: {message}");
                message.clear();
            }
        });
    }
}

```

### 监听端口

创建TCP监听器
例：
```rust
let lisntener = TcpListener::bind("localhost:8080").await.unwrap();
```

使用到了bind函数，输入一个字符串（地址，当前主机地址，加上一个端口号），被async修饰，所以用上await，又因为返回值为Result，如果返回Err，则panic。
``` rust
tokio::net::tcp::listener::TcpListener
pub async fn bind<A>(addr: A) -> io::Result<TcpListener>
where
    A: ToSocketAddrs,
A = &str

Creates a new TcpListener, which will be bound to the specified address.

The returned listener is ready for accepting connections.
```

### 循环等待客户端连接

非阻塞循环等待，可以支持多个多个客户端的接收与回传。

因为后面使用了`tokio::spawn`，启动一个新的异步任务，达到并发执行，不阻塞Echo Server，达到可以继续循环等待其他客户端连接。
``` rust
    loop {
        let (mut socket, addr) = lisntener.accept().await.unwrap();
        println!("{:#?}", socket);
        tokio::spawn(async move {
        ...
        }
    }
```

### 启动新的异步任务

使用`tokio::spawn`启动一个新的异步任务，达到不同异步任务间并发执行。
其中`async`后的`move`，用于将捕获到的变量move到异步任务中来。
其中为什么不能`move`，是因为TcpStream不支持Copy或者Clone。

原型：
``` rust
pub async fn accept(&self) -> Result<(TcpStream, SocketAddr)>
```
例：
``` rust
        let (mut socket, addr) = lisntener.accept().await.unwrap();
        println!("{:#?}", socket);
        tokio::spawn(async move {
            let (stream_reader, mut stream_writer) = socket.split();
```


### 分割读写流

``` rust
let (stream_reader: ReadHalf<'_>, mut stream_writer: WriteHalf<'_>) = socket.split();
```

- `socket.split()` 将 `TcpStream` 拆分为两个独立的流：
    - `stream_reader`：只读部分，用于读取客户端数据。
    - `stream_writer`：只写部分，用于向客户端发送数据。
- 这样可以实现非阻塞的读写分离。

### 初始化缓冲读取器

``` rust
let mut message: String = String::new();
let mut reader: BufReader<ReadHalf<'_>> = BufReader::new(inner: stream_reader);
```

- `message` 用于存储从客户端读取的一行文本。
- `BufReader` 包装 `stream_reader`，提供更高效的读取方式（比如 `read_line`）。

### 循环读取并回显消息

``` rust
loop {
    let bytes_read: usize = reader.read_line(buf: &mut message).await.unwrap();
    if bytes_read == 0 {
        break;
    }
    stream_writer.write_all(src: message.as_bytes()).await.unwrap();
    message.clear();
}
```

- `reader.read_line(&mut message)`：
    - 从客户端读取一行数据（以 `\n` 结尾），并将其追加到 `message` 字符串中。
    - 返回读取的字节数。
- `if bytes_read == 0`：
    - 如果没有读到任何字节，说明客户端关闭了连接，退出循环。
- `stream_writer.write_all(message.as_bytes())`：
    - 将读取到的消息原样写回客户端。
    - `.await.unwrap()` 等待写入完成。
- `message.clear()`：
    - 清空字符串，准备下一次读取。

> ⚠️ 注意：`write_all` 不会自动添加换行符，所以客户端收到的是原始内容（无换行）。如果希望回显带换行，应该写 `message.as_bytes()` 并手动加上 `\n`，或使用 `writeln!` 宏。

## ChatServer

### 添加广播


# 收获

