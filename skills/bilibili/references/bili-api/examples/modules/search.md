# 搜索功能 - 完整示例

## 搜索视频

```python
import asyncio
from bilibili_api import search
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    result = await search.search(
        keyword="Python教程",
        search_type=search.SearchObjectType.VIDEO,
        credential=credential
    )
    for item in result.get("result", []):
        print(f"标题: {item.get('title')}")
        print(f"BV号: {item.get('bvid')}")

asyncio.run(main())
```

## 搜索用户

```python
import asyncio
from bilibili_api import search
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    result = await search.search(
        keyword="老番茄",
        search_type=search.SearchObjectType.USER,
        credential=credential
    )
    for item in result.get("result", []):
        print(f"用户名: {item.get('uname')}")
        print(f"UID: {item.get('mid')}")
        print(f"粉丝数: {item.get('fans')}")

asyncio.run(main())
```

## 多条件搜索

```python
from bilibili_api import search, sync, video_zone

async def test_f_search_by_order():
    return await search.search_by_type(
        "小马宝莉",
        search_type=search.SearchObjectType.VIDEO,
        order_type=search.OrderVideo.SCORES,
        time_range=10,  # 10-30分钟
        video_zone_type=video_zone.VideoZoneTypes.DOUGA_MMD,
        page=1,
    )

res = sync(test_f_search_by_order())
print(res)
```

## 按粉丝数搜索用户

```python
from bilibili_api import search, sync

result = sync(
    search.search_by_type(
        "音乐",
        search_type=search.SearchObjectType.USER,
        order_type=search.OrderUser.FANS,
        order_sort=0,  # 0=从高到低
    )
)
print(result)
```
