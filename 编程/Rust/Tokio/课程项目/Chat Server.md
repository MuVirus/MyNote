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

### 功能学习
#### 添加广播通道

> 参考：
> https://docs.rs/tokio/1.48.0/tokio/sync/broadcast/index.html

这里broadcast用于多生产者多消费者，每个发送至都会广播给所有active接收方。

channel函数
``` rust
pub fn channel<T: Clone>(capacity: usize) -> (Sender<T>, Receiver<T>)
```

- capacity：表示容量


基本用途
``` rust
use tokio::sync::broadcast;

let (tx, mut rx1) = broadcast::channel(16);
let mut rx2 = tx.subscribe();

tokio::spawn(async move {
    assert_eq!(rx1.recv().await.unwrap(), 10);
    assert_eq!(rx1.recv().await.unwrap(), 20);
});

tokio::spawn(async move {
    assert_eq!(rx2.recv().await.unwrap(), 10);
    assert_eq!(rx2.recv().await.unwrap(), 20);
});

tx.send(10).unwrap();
tx.send(20).unwrap();
```

- 其中move表示将内部用到的rx1等其他的变量move进去。
- 这里广播容量为16，最开始就配有一个rx1，然后后来的rx2是tx使用subscribe得来的。

#### 获取ctrl+c signal

> 参考：
> https://docs.rs/tokio/1.48.0/tokio/signal/fn.ctrl_c.html

我们下面的示例中就直接为监听ctrl_c信号添加了一个异步任务。
``` rust
    tokio::spawn(async move {
        let sig = signal::ctrl_c().await;
        match sig {
            Ok(_) => {
                println!("Ctrl+C Signal!");
                cancel_token.cancel();
            }
            Err(e) => {
                println!("err:{:#?}", e);
            }
       
 }
    });
```

#### 取消令牌(Cancellation Token)

> 参考：
> https://docs.rs/tokio-util/0.7.17/tokio_util/sync/struct.CancellationToken.html

示例如下：

1. 可以看到其中使用`CancellationToken::new()`可以创建一个`取消令牌`，其中`CancellationToken`是可以`Clone`的。
2. 可以看到一般如果要`监测CancellationToken`或者`取消CancellationToken`一般都需要Clone一份，因为这些都在单独的异步任务当中。
3. `cloned_token.cancelled()`可以监测当前令牌是否取消了。
4. `token.cancel()`可以取消当前令牌。

``` rust
use tokio::select;
use tokio_util::sync::CancellationToken;

#[tokio::main]
async fn main() {
    let token = CancellationToken::new();
    let cloned_token = token.clone();

    let join_handle = tokio::spawn(async move {
        // Wait for either cancellation or a very long time
        select! {
            _ = cloned_token.cancelled() => {
                // The token was cancelled
                5
            }
            _ = tokio::time::sleep(std::time::Duration::from_secs(9999)) => {
                99
            }
        }
    });

    tokio::spawn(async move {
        tokio::time::sleep(std::time::Duration::from_millis(10)).await;
        token.cancel();
    });

    assert_eq!(5, join_handle.await.unwrap());
}
```

### ChatServer1

``` rust
use tokio::{
    io::{AsyncBufReadExt, AsyncWriteExt, BufReader}, net::TcpListener, select, sync::broadcast
};

#[tokio::main]
async fn main() {
    let lisntener = TcpListener::bind("localhost:8080").await.unwrap();
    let (tx, _) = broadcast::channel::<String>(10);
    loop {
        let (mut socket, addr) = lisntener.accept().await.unwrap();
        let tx = tx.clone();
        let mut rx = tx.subscribe();
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

                tx.send(message.clone()).unwrap();

                let receviced_message = rx.recv().await.unwrap();

                stream_writer.write_all(receviced_message.as_bytes()).await.unwrap();

                message.clear();
            }
        });
    }
}

```

缺陷

- 如果**只有一个客户端连接**，那么 `rx.recv()` 会收到自己刚发的消息 ✅（看似正常）
- 但如果**有多个客户端**：
    - 客户端 A 发 "hello"
    - 广播通道会把 "hello" 发送给 **A 的 rx 和 B 的 rx**
    - 但你的代码中，**A 只调用一次 `rx.recv()`**，它可能收到：
        - 自己发的 "hello"（期望）
        - **B 发的 "world"（非期望！）**

原因

``` rust
let bytes_read = reader.read_line(&mut message).await.unwrap();
```

该异步任务，必须要等到该客户端发送一行消息才往下执行，否则该异步任务被await阻塞。

改进点

只要rx有内容或者接收到客户端tcp消息内容，就执行。

### ChatServer2

``` rust
use tokio::{
    io::{AsyncBufReadExt, AsyncWriteExt, BufReader}, net::TcpListener, select, sync::broadcast
};

#[tokio::main]
async fn main() {
    let lisntener = TcpListener::bind("localhost:8080").await.unwrap();
    let (tx, _) = broadcast::channel(10);
    loop {
        let (mut socket, addr) = lisntener.accept().await.unwrap();
        let tx = tx.clone();
        let mut rx = tx.subscribe();
        tokio::spawn(async move {
            let (stream_reader, mut stream_writer) = socket.split();
            let mut message = String::new();
            let mut reader = BufReader::new(stream_reader);
            loop {
                tokio::select! {
                    read_result = reader.read_line(&mut message) => {
                        match read_result {
                            Ok(0) => {
                                break;
                            }
                            Ok(_) => {
                                tx.send((message.clone(), addr)).unwrap();
                                message.clear();
                            }
                            Err(e) => {
                                println!("{:#?}", e);
                            }
                        }
                    }
                    recv_result = rx.recv() => {
                        match recv_result {
                            Ok((msg, recv_addr)) => {
                                if recv_addr != addr {
                                    stream_writer.write_all(msg.as_bytes()).await.unwrap();                    
                                }
                            }
                            Err(e) => {
                                println!("{:#?}", e);
                            }
                        }
                    }
                }
            }
        });
    }
}

```

解决：
- 实现通过广播通信，不自己发送消息发送给自己。

缺陷：
- 退出如果用Ctrl+C，可能会导致报错。

### ChatServer3

``` rust
use tokio::{
    io::{AsyncBufReadExt, AsyncWriteExt, BufReader},
    net::TcpListener,
    select,
    sync::broadcast,
    signal,
};
use tokio_util::sync::CancellationToken;

#[tokio::main]
async fn main() {
    let lisntener = TcpListener::bind("localhost:8080").await.unwrap();
    let (tx, _) = broadcast::channel(10);
    let token = CancellationToken::new();
    let cancel_token = token.clone();
    tokio::spawn(async move {
        let sig = signal::ctrl_c().await;
        match sig {
            Ok(_) => {
                println!("Ctrl+C Signal!");
                cancel_token.cancel();
            }
            Err(e) => {
                println!("err:{:#?}", e);
            }
        }
    });
    loop {
        tokio::select! {
            accept_result = lisntener.accept() => {
                let (mut socket, addr) = accept_result.unwrap();
                println!("Client addr: {:#?}", addr);
                let tx = tx.clone();
                let mut rx = tx.subscribe();
                let token = token.clone();
                tokio::spawn(async move {
                    let (stream_reader, mut stream_writer) = socket.split();
                    let mut message = String::new();
                    let mut reader = BufReader::new(stream_reader);
                    loop {
                        tokio::select! {
                            read_result = reader.read_line(&mut message) => {
                                match read_result {
                                    Ok(0) => {
                                        break;
                                    }
                                    Ok(_) => {
                                        tx.send((message.clone(), addr)).unwrap();
                                        message.clear();
                                    }
                                    Err(e) => {
                                        println!("{:#?}", e);
                                    }
                                }
                            }
                            recv_result = rx.recv() => {
                                match recv_result {
                                    Ok((msg, recv_addr)) => {
                                        if recv_addr != addr {
                                            stream_writer.write_all(msg.as_bytes()).await.unwrap();
                                        }
                                    }
                                    Err(e) => {
                                        println!("{:#?}", e);
                                    }
                                }
                            }
                            _ = token.cancelled() => {
                                println!("Close Connection {addr}");
                                return;
                            }
                        }
                    }
                });
            }
            _ = token.cancelled() => {
                println!("Closing...");
                break;
            }
        }
    }
}

```

解决：
- 加入ctrl+c信号。
- 主程序也会受ctrl+c退出。

# 收获

