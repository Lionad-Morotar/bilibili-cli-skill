# 评论操作 - 完整示例

## 获取视频评论

```python
import asyncio
from bilibili_api import comment, video
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    bvid = "BV1xx411c7mD"
    v = video.Video(bvid=bvid, credential=credential)

    comments = await comment.get_comments(
        oid=await v.get_aid(),
        type_=comment.CommentResourceType.VIDEO,
        page=1,
        credential=credential
    )

    for c in comments.get("replies", []):
        print(f"用户: {c.get('member', {}).get('uname')}")
        print(f"内容: {c.get('content', {}).get('message')}")

asyncio.run(main())
```

## 获取所有评论（新接口）

```python
from bilibili_api import comment, sync

async def main():
    comments = []
    page = 1
    pag = ""

    while True:
        c = await comment.get_comments_lazy(
            418788911,
            comment.CommentResourceType.VIDEO,
            offset=pag
        )
        pag = c["cursor"]["pagination_reply"]["next_offset"]
        replies = c['replies']
        if replies is None:
            break
        comments.extend(replies)
        page += 1
        if page > 5:
            break

    for cmt in comments:
        print(f"{cmt['member']['uname']}: {cmt['content']['message']}")

sync(main())
```

## 发表评论

```python
import asyncio
from bilibili_api import comment, video
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    bvid = "BV1xx411c7mD"
    v = video.Video(bvid=bvid, credential=credential)

    result = await comment.send_comment(
        text="这是一条测试评论",
        oid=await v.get_aid(),
        type_=comment.CommentResourceType.VIDEO,
        credential=credential
    )
    print(f"评论ID: {result.get('rpid')}")

asyncio.run(main())
```

## 点赞评论

```python
import asyncio
from bilibili_api import comment
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    result = await comment.like_comment(
        oid=987654321,
        rpid=123456789,
        type_=comment.CommentResourceType.VIDEO,
        status=True,
        credential=credential
    )
    print("点赞成功！")

asyncio.run(main())
```
