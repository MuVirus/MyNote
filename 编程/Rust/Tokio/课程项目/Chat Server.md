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

使用到了bind函数，输入一个字符串（地址，当前主机地址，加上一个端口号）
``` rust
tokio::net::tcp::listener::TcpListener
pub async fn bind<A>(addr: A) -> io::Result<TcpListener>
where
    A: ToSocketAddrs,
A = &str

Creates a new TcpListener, which will be bound to the specified address.

The returned listener is ready for accepting connections.
```
# 收获

