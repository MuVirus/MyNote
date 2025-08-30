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
# 5、过滤修饰符
在字段后加上两个下划线`(_)`，然后加上修饰符
只支持PostgreSQL和MYSQL。
```
过滤¶
当使用.filter()方法时，您可以使用字段名称的多个修饰符来指定所需的操作

teams = await Team.filter(name__icontains='CON')

not

in - 检查字段值是否在传入的列表中

not_in

gte - 大于或等于传入的值

gt - 大于传入的值

lte - 小于或等于传入的值

lt - 小于传入的值

range - 在给定的两个值之间

isnull - 字段为 null

not_isnull - 字段不为 null

contains - 字段包含指定的子字符串

icontains - 不区分大小写的 contains

startswith - 如果字段以值开头

istartswith - 不区分大小写的 startswith

endswith - 如果字段以值结尾

iendswith - 不区分大小写的 endswith

iexact - 不区分大小写的等于

search - 全文搜索
```