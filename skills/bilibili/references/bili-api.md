# bilibili-api 使用指南

当 [bilibili-cli](https://github.com/jackwener/bilibili-cli) 无法满足需求时，可使用 [bilibili-api](https://nemo2011.github.io/bilibili-api) 库。

## 目录

- [安装](#安装)
- [快速开始](#快速开始)
- [凭证构造](#凭证构造)
- [配置](#配置)
- [核心模块](#核心模块)
- [更多模块](#更多模块)
- [错误处理](#错误处理)
- [注意事项](#注意事项)

---

## 安装

```bash
pip3 install bilibili-api-python aiohttp
```

## 快速开始

### 异步执行

```python
import asyncio
from bilibili_api import video

async def main():
    v = video.Video(bvid="BV1uv411q7Mv")
    info = await v.get_info()
    print(info)

asyncio.run(main())
```

### 同步执行（使用 sync）

```python
from bilibili_api import video, sync

v = video.Video(bvid="BV1uv411q7Mv")
info = sync(v.get_info())
print(info)
```

---

## 凭证构造

```bash
python ./references/bili-api/get_credential.py
```

```python
from references.bili-api.get_credential import get_credential
credential = get_credential()
```

### 或从浏览器获取凭证

打开 B 站，F12 打开开发者工具：

**Edge/Chrome:** 应用程序 → 存储/Cookies → 选择 b 站域名

复制以下字段：
- `SESSDATA` - 获取用户信息
- `bili_jct` - 修改数据（点赞、评论）
- `buvid3` / `buvid4` - 设备验证码
- `DedeUserID` - 用户 UID
- `ac_time_value` - 刷新 Cookies（控制台输入 `window.localStorage.ac_time_value`）

---

## 配置

### 代理设置

```python
from bilibili_api import request_settings
request_settings.set_proxy("http://your-proxy.com")
```

### 超时设置

```python
request_settings.set_timeout(30.0)
```

### 请求日志

```python
from bilibili_api import request_log
request_log.set_on(True)
```

### 请求库选择

```python
from bilibili_api import select_client
select_client("curl_cffi")  # 支持伪装浏览器
select_client("aiohttp")
select_client("httpx")  # 不支持 WebSocket
```

---

## 核心模块

### 动态操作

**发送图文动态：**

```python
import asyncio
from bilibili_api import dynamic
from bilibili_api.utils.picture import Picture
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    picture = Picture.from_file("/path/to/image.jpg")

    build = dynamic.BuildDynamic.empty()
    build.add_plain_text("图文动态内容")
    build.add_image(picture)

    result = await dynamic.send_dynamic(info=build, credential=credential)
    print(f"动态ID: {result['dyn_id']}")

asyncio.run(main())
```

**完整示例：** [modules/dynamic.md](examples/modules/dynamic.md)

---

### 视频操作

**获取视频信息：**

```python
import asyncio
from bilibili_api import video
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    v = video.Video(bvid="BV1xx411c7mD", credential=credential)
    info = await v.get_info()
    print(f"标题: {info.get('title')}")
    print(f"播放量: {info.get('stat', {}).get('view')}")

asyncio.run(main())
```

**点赞/投币/三连：**

```python
await v.like(status=True)      # 点赞
await v.pay_coin(num=1)        # 投币
await v.triple()               # 一键三连
```

**完整示例：** [modules/video.md](examples/modules/video.md)

---

### 用户信息

**获取用户信息：**

```python
import asyncio
from bilibili_api import user
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    u = user.User(uid=6626299, credential=credential)
    info = await u.get_user_info()
    print(f"用户名: {info.get('name')}")
    print(f"粉丝数: {info.get('fans')}")

asyncio.run(main())
```

**完整示例：** [modules/user.md](examples/modules/user.md)

---

### 搜索功能

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

**完整示例：** [modules/search.md](examples/modules/search.md)

---

### 评论操作

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

**完整示例：** [modules/comment.md](examples/modules/comment.md)

---

### 其他核心模块

| 模块 | 功能概述 | 完整示例 |
|------|----------|----------|
| 收藏夹 | 获取/管理收藏夹 | [modules/favorite_list.md](examples/modules/favorite_list.md) |
| 直播 | 弹幕监听、礼物、自动回复 | [modules/live.md](examples/modules/live.md) |
| 番剧 | 剧集 BV 号、评论、下载 | [modules/bangumi.md](examples/modules/bangumi.md) |
| 图文 | Opus 内容获取、点赞 | [modules/opus.md](examples/modules/opus.md) |
| 专栏 | 转 Markdown、三连 | [modules/article.md](examples/modules/article.md) |

---

## 更多模块

以下模块的详细示例见 `examples/` 目录：

| 模块 | 说明 | 模块 | 说明 |
|------|------|------|------|
| [activity](examples/activity.md) | 活动 | [app](examples/app.md) | 应用程序 |
| [article_category](examples/article_category.md) | 专栏分类 | [ass](examples/ass.md) | ASS 弹幕/字幕 |
| [audio](examples/audio.md) | 音频 | [audio_uploader](examples/audio_uploader.md) | 音频上传 |
| [black_room](examples/black_room.md) | 小黑屋 | [channel_series](examples/channel_series.md) | 合集与列表 |
| [cheese](examples/cheese.md) | 课程 | [client](examples/client.md) | 终端 |
| [creative_center](examples/creative_center.md) | 创作中心 | [emoji](examples/emoji.md) | 表情包 |
| [festival](examples/festival.md) | 节日/拜年祭 | [game](examples/game.md) | 游戏 |
| [garb](examples/garb.md) | 装扮/收藏集 | [homepage](examples/homepage.md) | 主页 |
| [hot](examples/hot.md) | 热门 | [interactive_video](examples/interactive_video.md) | 互动视频 |
| [live_area](examples/live_area.md) | 直播分区 | [login_v2](examples/login_v2.md) | 登录(v2) |
| [manga](examples/manga.md) | 漫画 | [music](examples/music.md) | 音乐 |
| [note](examples/note.md) | 笔记 | [rank](examples/rank.md) | 排行榜 |
| [session](examples/session.md) | 私信会话 | [show](examples/show.md) | 展出 |
| [topic](examples/topic.md) | 话题 | [video_tag](examples/video_tag.md) | 视频标签 |
| [video_uploader](examples/video_uploader.md) | 视频上传 | [video_zone](examples/video_zone.md) | 视频分区 |
| [vote](examples/vote.md) | 投票 | [watchroom](examples/watchroom.md) | 放映室 |

---

## 错误处理

```python
import asyncio
from bilibili_api import dynamic, exceptions
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    try:
        dyn = dynamic.Dynamic(dynamic_id="invalid_id", credential=credential)
        info = await dyn.get_info()
    except exceptions.CredentialNoSessdataException:
        print("错误：缺少 sessdata")
    except exceptions.CredentialNoBiliJctException:
        print("错误：缺少 bili_jct")
    except exceptions.ResponseCodeException as e:
        print(f"API返回错误：{e.code} - {e.msg}")
    except Exception as e:
        print(f"未知错误：{e}")

asyncio.run(main())
```

---

## 注意事项

1. **异步操作**：全部为异步 API，使用 `async/await` 或 `sync()` 函数
2. **请求频率**：请求过快会触发 412 错误，添加延迟或使用代理
3. **凭证安全**：不要泄露 `SESSDATA` 和 `bili_jct`
4. **buvid3 缺失**：可启用自动生成：`request_settings.set_enable_auto_buvid(True)`
5. **ac_time_value**：用于刷新 Cookies，没有则需重新登录

## 相关链接

- [bilibili-api 官方文档](https://nemo2011.github.io/bilibili-api)
- [GitHub 仓库](https://github.com/nemo2011/bilibili-api)
