# 查询
参考：[Query API - Tortoise ORM v0.25.1 Documentation](https://tortoise.github.io/query.html)
## 1、get
```
get(*args, **kwargs)¶
获取与参数匹配的单个对象。

返回类型¶
QuerySetSingle[Model]
```
## 2、all
```
all()¶
返回整个 QuerySet。除了作为唯一操作之外，本质上是无操作。

返回类型¶
QuerySet[Model]
```
## 3、filter
```
filter(*args, **kwargs)¶
按给定的 kwargs 过滤 QuerySet。您可以像这样按相关对象进行过滤

Team.filter(events__tournament__name='Test')
您还可以将 Q 对象作为 args 传递给过滤器。

返回类型¶
QuerySet[Model]
```
## 4、exclude
```
exclude(*args, **kwargs)¶
与 .filter() 相同，但会将所有参数追加到 NOT

返回类型¶
QuerySet[Model]
```