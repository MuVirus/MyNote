> 课程：Async Rust（软件工艺师）

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

通过Tcp通信，客户端可以向服务器发送数据，然后服务器将数据返回。可以使用telnet、nc工具当作客户端来使用。
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

# 收获

