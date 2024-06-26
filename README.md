# Redis Sesion Implementation for Naja Atra

This is the redis session implementation for the project [naja atra](https://github.com/naja-atra/naja-atra)

## Usage

```python
import naja_atra
import naja_atra.server as server
import os
from naja_atra_redis_session.redis_session import RedisSessionFactory

def main(*args):
    # host, port, db, username, password are all optional.
    naja_atra.set_session_factory(RedisSessionFactory(host="10.0.2.16", port=6379, db=0, username="", password=""))
    server.scan("tests/ctrls", r'.*controllers.*')
    
    root = os.path.dirname(os.path.abspath(__file__))
    server.start(
        port=9090,
        resources={"/public/*": f"{root}/tests/static"})

```

or you can re-use your redis client which might be used in other codes.

```python
import naja_atra
import naja_atra.server as server
import os
import redis
from naja_atra_redis_session.redis_session import RedisSessionFactory

def main(*args):
    # This redis client can be used in other business codes.
    redis_client = redis.Redis(host="10.0.2.16", port=6379, db=0, username="", password="")
    naja_atra.set_session_factory(RedisSessionFactory(redis_client=redis_client))
    
    root = os.path.dirname(os.path.abspath(__file__))
    server.start(
        port=9090,
        resources={"/public/*": f"{root}/tests/static"})

```

## Write Your Own ObjectSerializer

Module `pickle` is used to do the serialization and deserialization, if the defalut serialization logic could not satisfy you, you can write your own

```python
naja_atra_redis_session.redis_session import ObjectSerializer

class MyObjectSerializer(ObjectSerializer):

    def object_to_bytes(self, obj: Any) -> bytes:
        bys = ...
        return bys

    def bytes_to_objects(self, value: bytes) -> Any:
        obj = ...
        return obj

# Set it when initializing a SessionFactory.

naja_atra.set_session_factory(RedisSessionFactory(host="10.0.2.16", port=6379, db=0, username="", password="", object_serializer=MyObjectSerializer()))
```


