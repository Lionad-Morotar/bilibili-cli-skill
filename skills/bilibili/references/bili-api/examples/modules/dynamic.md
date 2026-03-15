# 动态操作 - 完整示例

## 发送图文动态

```python
import asyncio
from bilibili_api import dynamic
from bilibili_api.utils.picture import Picture
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    picture = Picture.from_file("/path/to/image.jpg")

    build = dynamic.BuildDynamic.empty()
    build.add_plain_text("这是一条图文动态")
    build.add_image(picture)

    result = await dynamic.send_dynamic(info=build, credential=credential)
    print(f"动态ID: {result['dyn_id']}")

asyncio.run(main())
```

## 发送纯文本动态

```python
import asyncio
from bilibili_api import dynamic
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    result = await dynamic.send_text("Hello from bilibili-api!", credential)
    print(f"动态ID: {result['dyn_id']}")

asyncio.run(main())
```

## 定时发送动态

```python
from bilibili_api import dynamic, Credential, sync
import datetime

async def main():
    dy = (
        dynamic.BuildDynamic()
        .add_text("定时动态内容")
        .add_at(uid=1)
        .add_emoji("[tv_doge]")
        .set_send_time(datetime.datetime(2025, 2, 1, 0, 0, 0))
    )
    await dynamic.send_dynamic(dy, credential=Credential(sessdata="", bili_jct=""))

sync(main())
```

## 获取动态详情

```python
import asyncio
from bilibili_api import dynamic
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    dyn = dynamic.Dynamic(dynamic_id="1179493479458275334", credential=credential)
    info = await dyn.get_info()
    print(info)

asyncio.run(main())
```

## 获取自己的动态列表

```python
import asyncio
from bilibili_api import dynamic
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    result = await dynamic.get_dynamic_page_info(
        uid=int(credential.dedeuserid),
        credential=credential
    )
    for item in result.get("items", []):
        print(f"动态ID: {item.get('id_str')}")

asyncio.run(main())
```

## 点赞/删除动态

```python
import asyncio
from bilibili_api import dynamic
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    dyn = dynamic.Dynamic(dynamic_id="1179493479458275334", credential=credential)

    # 点赞
    await dyn.set_like(status=True)

    # 删除
    # await dyn.delete()

asyncio.run(main())
```

## 转发抽奖示例

```python
from bilibili_api import dynamic, sync
import random

DYNAMIC_ID = 0
COUNT = 3

async def main():
    dy = dynamic.Dynamic(DYNAMIC_ID)
    reposters = []
    offset = "0"

    while True:
        r = await dy.get_reposts(offset)
        reposters.extend(r['items'])
        if r['has_more'] != 1:
            break
        offset = r['offset']

    lucky_dogs = []
    for i in range(COUNT):
        index = random.randint(0, len(reposters) - 1)
        lucky_dogs.append(reposters[index])
        reposters.remove(reposters[index])

    print('中奖名单：')
    for p in lucky_dogs:
        print(f'{p["desc"]["user_profile"]["info"]["uname"]}')

sync(main())
```
