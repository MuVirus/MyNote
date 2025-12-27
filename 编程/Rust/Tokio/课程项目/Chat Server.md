> 课程：Async Rust（软件工艺师）

# 

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
# 收获

