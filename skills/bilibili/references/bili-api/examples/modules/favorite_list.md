# 收藏夹操作 - 完整示例

## 获取视频收藏夹列表

```python
import asyncio
from bilibili_api import favorite_list
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    fav_list = await favorite_list.get_video_favorite_list(
        uid=int(credential.dedeuserid),
        credential=credential
    )
    for item in fav_list.get("list", []):
        print(f"收藏夹: {item.get('title')}")
        print(f"ID: {item.get('id')}")
        print(f"数量: {item.get('media_count')}")

asyncio.run(main())
```

## 获取收藏夹内容

```python
import asyncio
from bilibili_api import favorite_list
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    media_id = 123456
    fav = favorite_list.FavoriteList(media_id=media_id, credential=credential)
    content = await fav.get_content(page=1)

    for item in content.get("medias", []):
        print(f"标题: {item.get('title')}")
        print(f"BV号: {item.get('bvid')}")

asyncio.run(main())
```
