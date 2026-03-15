# 专栏操作 - 完整示例

## 获取专栏内容并转换为 Markdown

```python
from bilibili_api import article, sync

async def main():
    ar = article.Article(15160286)
    if ar.is_note():
        ar = ar.turn_to_note()
    await ar.fetch_content()
    with open('article.md', 'w', encoding='utf8') as f:
        f.write(ar.markdown())

sync(main())
```

## 专栏三连

```python
from bilibili_api import *

async def main():
    ar = article.Article(2, credential=Credential(
        sessdata="",
        bili_jct=""
    ))
    await ar.set_like(status=True)
    await ar.add_coins()
    await ar.set_favorite(status=True)

sync(main())
```
