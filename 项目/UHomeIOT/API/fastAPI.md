# 静态文件
## 基本用法
您可以使用 `StaticFiles`从目录中自动提供静态文件。
- 导入`StaticFiles`。
- "挂载"(Mount) 一个 `StaticFiles()` 实例到一个指定路径。
``` python
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles

app = FastAPI()

app.mount("/static", StaticFiles(directory="static"), name="static")
```
## 拓展用法
### 1、如何提供其他层次文件夹的静态文件？
比如使用上一级image文件夹的图片
给directory赋值给images_url。
``` python
images_url = Path(__file__).parent / "images"
```
