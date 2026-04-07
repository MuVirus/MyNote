

## 异步
[C++ 异步编程（future、promise、packaged_task、async） - 指南 - jzssuanfa - 博客园](https://www.cnblogs.com/jzssuanfa/p/19247528#1-%E5%BC%82%E6%AD%A5%E7%BC%96%E7%A8%8B%E5%9F%BA%E7%A1%80)

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
### future
`std::future` 是一个模板类，代表一个**异步操作的未来结果**。你可以把它想象成一张"提货单"：
- 现在可能还没有结果
- 将来某个时间点结果会准备好
- 可以随时查询结果是否就绪
- 可以等待并获取结果
**核心特性：**
- 只能移动，不能复制
- `get()` 只能调用一次
- 自动等待异步任务完成
### promise
`std::promise` 是结果的**提供者**，它允许你在一个线程中设置值或异常，然后在另一个线程中通过 `std::future` 获取。
**promise 和 future 的关系：**
```css
线程A: promise.set_value(42)  →  共享状态  →  线程B: future.get() 返回 42
```

**核心特性**
- 一次性使用：只能设置一次值
- 配对使用：promise 和 future 配对
- 异常传递：可以传递异常
**基本步骤：**
1. 创建 `std::promise<T>` 对象
2. 通过 `get_future()` 获取关联的 `std::future`
3. 将 promise 传递给工作线程（使用 `std::ref()`）
4. 工作线程调用 `set_value()` 或 `set_exception()`
5. 主线程通过 future 的 `get()` 获取结果

### packaged_task
`std::packaged_task` 是一个**可调用对象的包装器**，它将函数、lambda 或函数对象包装成一个异步任务，可以在任何时候执行。
**与 promise 的区别：**
- `promise`：手动设置值
- `packaged_task`：自动从函数返回值设置
**基本步骤：**
1. 创建 `std::packaged_task<返回类型(参数类型)>` 对象
2. 通过 `get_future()` 获取关联的 future
3. 像普通函数一样调用 task（可以在任何线程）
4. 通过 future 获取结果