# Mangum

<a href="https://pypi.org/project/mangum/">
    <img src="https://badge.fury.io/py/mangum.svg" alt="Package version">
</a>
<img alt="PyPI - Python Version" src="https://img.shields.io/pypi/pyversions/mangum.svg?style=flat-square">

Mangum is an adapter for running [ASGI](https://asgi.readthedocs.io/en/latest/) applications in AWS Lambda to handle Function URL, API Gateway, ALB, and Lambda@Edge events.

***Documentation***: https://mangum.fastapiexpert.com/

## Features

- Event handlers for API Gateway [HTTP](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html) and [REST](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-rest-api.html) APIs, [Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/lambda-functions.html), [Function URLs](https://docs.aws.amazon.com/lambda/latest/dg/lambda-urls.html), and [CloudFront Lambda@Edge](https://docs.aws.amazon.com/lambda/latest/dg/lambda-edge.html).

- Compatibility with ASGI application frameworks, such as [Starlette](https://www.starlette.io/), [FastAPI](https://fastapi.tiangolo.com/), [Quart](https://pgjones.gitlab.io/quart/) and [Django](https://www.djangoproject.com/).

- Support for binary media types and payload compression in API Gateway using GZip or Brotli.

- Works with existing deployment and configuration tools, including [Serverless Framework](https://www.serverless.com/) and [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html).

- Startup and shutdown [lifespan](https://asgi.readthedocs.io/en/latest/specs/lifespan.html) events.

## Installation

```shell
pip install mangum
```

## Example

```python
from mangum import Mangum

async def app(scope, receive, send):
    await send(
        {
            "type": "http.response.start",
            "status": 200,
            "headers": [[b"content-type", b"text/plain; charset=utf-8"]],
        }
    )
    await send({"type": "http.response.body", "body": b"Hello, world!"})


handler = Mangum(app, lifespan="off")
```

Or using a framework:

```python
from fastapi import FastAPI
from mangum import Mangum

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}

handler = Mangum(app, lifespan="off")
```
