

# Asynchronous
## 1、Future
### 1) `Future.then`
异步操作完成时（成功）调用的回调函数。
### 2) `Future.catchError`
处理Future发出的错误。
### 3) `Future.whenComplete`
异步操作完成时调用的函数。
异步操作不论是否有异常，都会运行该函数。
### 4) `Future.wait`
等待多个Future完成并收集其结果。
示例：
``` Dart
Future.wait([
	Future.delayed(Duration(seconds: 2), () {
		return "hello";
	}),
	Future.delayed(Duration(seconds: 4), () {
	})
]).then((results) {
	print(results[0] + results[1]);
}).catchError((e) {
	print(e);
});
```
### 5) Future.delayed
创建一个在延迟后执行其计算的异步操作
``` dart
Future.delayed(Duration duration, [FutureOr<T> computation()?])
```
示例：
通过Duration延迟2秒，然后调用第二个匿名函数，通过then（Future类）函数接收变量，然后打印出来。
``` Dart
Future.delayed(Duration(seconds: 2), () {
	return "hello";
}).then((data) {
	print(data);
});
```