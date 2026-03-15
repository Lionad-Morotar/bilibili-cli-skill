# 用户信息 - 完整示例

## 获取当前登录用户信息

```python
import asyncio
from bilibili_api import user
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    u = user.User(uid=int(credential.dedeuserid), credential=credential)
    info = await u.get_user_info()
    print(info)

asyncio.run(main())
```

## 获取指定用户信息

```python
import asyncio
from bilibili_api import user
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    target_uid = 6626299
    u = user.User(uid=target_uid, credential=credential)
    info = await u.get_user_info()
    print(f"用户名: {info.get('name')}")
    print(f"粉丝数: {info.get('fans')}")
    print(f"关注数: {info.get('following')}")

asyncio.run(main())
```

## 获取用户视频列表

```python
import asyncio
from bilibili_api import user
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    target_uid = 6626299
    u = user.User(uid=target_uid, credential=credential)
    videos = await u.get_videos(page=1)

    for video in videos.get("list", {}).get("vlist", []):
        print(f"标题: {video.get('title')}")
        print(f"BV号: {video.get('bvid')}")
        print(f"播放量: {video.get('play')}")

asyncio.run(main())
```

## 获取用户所有动态

```python
import asyncio
from bilibili_api import user

async def main():
    u = user.User(660303135)
    offset = ""
    dynamics = []

    while True:
        page = await u.get_dynamics_new(offset)
        dynamics.extend(page["items"])
        if page["has_more"] != 1:
            break
        offset = page["offset"]

    print(f"共有 {len(dynamics)} 条动态")

asyncio.run(main())
```

## 获取共同关注

```python
import asyncio
from bilibili_api import user, Credential

async def main():
    credential = Credential(sessdata="xxxxxxxxxx")
    bishi = user.User(uid=2, credential=credential)
    lists = await bishi.get_self_same_followers()
    for up in lists["list"]:
        print(up["uname"])

asyncio.run(main())
```
