# 图文操作 - 完整示例

## 获取图文信息

```python
import asyncio
from bilibili_api import opus
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    op = opus.Opus(opus_id=123456789, credential=credential)

    info = await op.get_info()
    print(info)

    images = await op.get_images()
    for img in images:
        print(img.url)

asyncio.run(main())
```

## 图文点赞/收藏/投币

```python
import asyncio
from bilibili_api import opus
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    op = opus.Opus(opus_id=123456789, credential=credential)

    await op.set_like(status=True)
    await op.set_favorite()
    await op.add_coins()

asyncio.run(main())
```

## 图文转 Markdown

```python
import asyncio
from bilibili_api import opus

async def main():
    op = opus.Opus(opus_id=123456789)
    md = await op.markdown()
    print(md)

asyncio.run(main())
```
