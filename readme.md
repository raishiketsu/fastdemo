### 1.Create Dockerfile
```
vi Dockerfile
```

```
FROM tiangolo/uvicorn-gunicorn-fastapi:python3.9

COPY ./requirements.txt /app/requirements.txt

RUN pip install --no-cache-dir --upgrade -r /app/requirements.txt

COPY ./app /app
```

### 2.Create requirements.txt
```
vi requirements.txt
```
```
fastapi
```

### 3.Create app directory
```
mkdir app
```

### 4.Create main.py
```
cd app
vi main.py
```

```
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}
```



### 5.Directory
```
.
├── app
│   └── main.py
└── Dockerfile
└── requirements.txt
```

### 6.Docker Build
※Dockerfileと同じディレクトリで実行し、
イメージを作成

```
docker build -t myimage .
```
### 6.Docker Run
コンテナを起動
```
docker run -d --name mycontainer -p 80:80 myimage
curl http://localhost
```
### 7.Push
＊＊＊＊＊＊の部分はコンテナレジストリに置き換えます
```
docker login

docker tag myimage ＊＊＊＊＊＊/fastdemo:v1.0
docker push ＊＊＊＊＊＊/fastdemo:v1.0
```

---

### Create YAML
```
apiVersion: managed.msp.sbopsv/v1alpha1
kind: Application
metadata:
  name: fastdemo
  namespace: staging
spec:
  chart:
    name: basic-deployment
    version: 0.4.x
  settings:
    image:
      repository: rais39/fastdemo
      pullPolicy: IfNotPresent
      tag: "v1.0"
    service:
      domain: fastdemo.sb-presales.com
      serviceType: LoadBalancer
      ports:
      - name: fastdemo
        containerPort: 80
        servicePort: 80
```

---

### Update
main.pyを更新して、イメージをアップデートし新しいタグv1.1を付けます。
```
from typing import Union

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    price: float
    is_offer: Union[bool, None] = None


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str, None] = None):
    return {"item_id": item_id, "q": q}


@app.put("/items/{item_id}")
def update_item(item_id: int, item: Item):
    return {"item_name": item.name, "item_id": item_id}
```


