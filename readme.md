https://github.com/tiangolo/uvicorn-gunicorn-fastapi-docker#build-your-image

### Dockerfile

```
FROM tiangolo/uvicorn-gunicorn-fastapi:python3.9

COPY ./requirements.txt /app/requirements.txt

RUN pip install --no-cache-dir --upgrade -r /app/requirements.txt

COPY ./app /app
```

### app directory 

### main.py

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

### requirements.txt
```
fastapi
```

### Directory
```
.
├── app
│   └── main.py
└── Dockerfile
└── requirements.txt
```

### Build
```
docker build -t myimage .
```
### Run
```
docker run -d --name mycontainer -p 80:80 myimage
curl http://localhost
```
### Push
```
docker login

docker tag myimage rais39/fastdemo:v1.2
docker push rais39/fastdemo:v1.2
```

### Update
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