# 语法核心
## 一、变量
### 1、late

late 修饰符有两种用法：
- 声明一个非空变量，但不在声明时初始化。
- 延迟初始化一个变量。

> [!notice]
> 如果一开始没有初始化late变量，那么使用前必须赋值

在声明时就初始化了`late`变量，那么就起到了延迟初始化`late`变量的作用了。只会在使用到`late`变量，才会初始化变量。

### 2、final / const

一个 final 变量只能设置一次，const 变量是编译时常量。（const 常量隐式包含了 final，也就是说 const 自带 final 的消息）
> [!注意]
> 实例变量 可以是 final 但不能是 const。

### 3、空安全

1. 不可空（**Non-nullable**）
```dart
int a = 5;
a = null;          // ❌ 编译期就报错
```

2. 可空（**Nullable**）
```dart
int? b = 5;
b = null;          // ✅ 允许
```

3. 空感知运算符
- `?.` 链式调用，空时短路返回 `null`
- `??` 空时给默认值
- `??=` 空时才赋值
- `!` **非空断言**：你向编译器保证“绝对非空”，运行时空了就抛异常

``` dart
int? x;
print(x?.toRadixString(16));  // null
print(x ?? 42);               // 42
x ??= 100;                    // 此时 x = 100
print(x!.toRadixString(16));  // 64，若 x 为 null 直接抛运行时异常
```

4. 延迟初始化关键字
- `late`：告诉编译器“我现在不赋值，但使用前一定非空”，运行时空了会抛 `LateInitializationError`。
- `late final`：只能赋值一次，常用于依赖注入或懒加载。

```dart
late String name;             // 先声明，稍后赋值
name = 'Tom';                 // 使用前必须赋过值
```

5. 列表/Map 的可空性

```dart
List<int> nums = [];        // 列表本身及其元素都不可空
List<int?> numsWithNull = [1, null];  // 元素可空
List<int>? nullableList;    // 列表本身可空，元素不可空
List<int?>? bothNullable;   // 列表和元素都可空
```

- `[]` 为空列表。