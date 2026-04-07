

## 异步

| 组件                     | 角色    | 使用场景               |
| ---------------------- | ----- | ------------------ |
| **std::future**        | 结果接收者 | 获取异步操作的结果          |
| **std::promise**       | 结果提供者 | 在一个线程中设置结果，另一个线程获取 |
| **std::packaged_task** | 任务包装器 | 将函数包装为异步任务         |
| **std::async**         | 任务启动器 | 最简单的启动异步任务的方式      |
```cpp
std::async → 返回 → std::future
std::promise → 创建 → std::future
std::packaged_task → 获取 → std::future
```