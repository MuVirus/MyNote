# 静态文件
您可以使用 `StaticFiles`从目录中自动提供静态文件。
- 导入`StaticFiles`。
- "挂载"(Mount) 一个 `StaticFiles()` 实例到一个指定路径。
``` python
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles

app = FastAPI()

app.mount("/static", StaticFiles(directory="static"), name="static")
```