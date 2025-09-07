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

# 依赖项
## 类作为依赖项


# 安全性
## OAuth2
### OAuth2 实现简单的 Password 和 Bearer 验证

``` python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from typing import Annotated
from pydantic import BaseModel
import uvicorn

# 伪数据库(内存中，重启及丢失)
fake_users_db = {
    "johndoe": {
        "username": "johndoe",
        "email": "johndoe@example.com",
        "hashed_password": "fakehashedsecret",
        "disabled": False,
    },
    "alice": {
        "username": "alice",
        "email": "alice@example.com",
        "hashed_password": "fakehashedsecret2",
        "disabled": True,
    },
}

app = FastAPI()

# 伪哈希生成器
def fake_hash_password(password: str):
    return "fakehashed" + password

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# User模型(Pydantic)
class User(BaseModel):
    username: str
    email: str | None = None
    disabled: bool | None = None

# 加上(哈希加密)密码的User模型
class UserInDB(User):
    hashed_password: str

def get_user(db, username: str):
    if username in db:
        user_dict = db[username]
        return UserInDB(**user_dict)

# 伪解码器
def fake_decode_token(token):
    user = get_user(fake_users_db, token)
    return user

async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)]):
    user = fake_decode_token(token)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials",
            headers={"WWW-Authenticate": "Bearer"},
        )
    return user

async def get_current_active_user(current_user: Annotated[User, Depends(get_current_user)]):
    if current_user.disabled:
        raise HTTPException(status_code=400, detail="Incorrect user")
    return current_user

@app.post("/token")
async def login(form_data: Annotated[OAuth2PasswordRequestForm, Depends()]):
    user_dict = fake_users_db.get(form_data.username)
    if not user_dict:
        raise HTTPException(status_code=400, detail="Incorrect username or password")
    user = UserInDB(**user_dict)
    hashed_password = fake_hash_password(form_data.password)
    if not hashed_password == user.hashed_password:
        raise HTTPException(status_code=400, detail="Incorrect username or password")
    return {"access_token": user.username, "token_type": "bearer"}

@app.get("/users/me")
async def read_items(current_user: Annotated[str, Depends(get_current_active_user)]):
    return current_user

if __name__ == "__main__":
    uvicorn.run("main:app", host="127.0.0.1", port=8686, reload=True)

```

### OAuth2 实现密码哈希与 Bearer JWT 令牌验证

#### 1、库介绍
##### 1) 生成和校验 JWT 令牌

```
pip install pyjwt
```
##### 2) 密码哈希

```
pip install passlib
```
官方推荐算法是`Bcrypt`

```
pip install passlib[bcrypt]
```


``` python

```