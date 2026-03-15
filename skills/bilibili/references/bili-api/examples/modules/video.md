# 视频操作 - 完整示例

## 获取视频信息

```python
import asyncio
from bilibili_api import video
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    v = video.Video(bvid="BV1xx411c7mD", credential=credential)
    info = await v.get_info()
    print(f"标题: {info.get('title')}")
    print(f"UP主: {info.get('owner', {}).get('name')}")
    print(f"播放量: {info.get('stat', {}).get('view')}")

asyncio.run(main())
```

## 获取弹幕

```python
import asyncio
from bilibili_api import video, sync

async def main():
    v = video.Video(bvid="BV1xx411c7mD")
    danmakus = await v.get_danmakus(page=1)
    for dm in danmakus:
        print(f"[{dm.time}] {dm.text}")

sync(main())
```

## 点赞/投币/一键三连

```python
import asyncio
from bilibili_api import video
from references.bili-api.get_credential import get_credential

async def main():
    credential = get_credential()
    v = video.Video(bvid="BV1xx411c7mD", credential=credential)

    # 点赞
    await v.like(status=True)

    # 投币 (1或2个)
    await v.pay_coin(num=1)

    # 一键三连
    await v.triple()

asyncio.run(main())
```

## 下载视频

```python
import asyncio
import os
from bilibili_api import video, Credential, HEADERS, get_client

SESSDATA = ""
BILI_JCT = ""
BUVID3 = ""
FFMPEG_PATH = "ffmpeg"

async def download(url: str, out: str, intro: str):
    dwn_id = await get_client().download_create(url, HEADERS)
    bts = 0
    tot = get_client().download_content_length(dwn_id)
    with open(out, "wb") as file:
        while True:
            bts += file.write(await get_client().download_chunk(dwn_id))
            print(f"{intro} - {out} [{bts} / {tot}]", end="\r")
            if bts == tot:
                break
    print()

async def main():
    credential = Credential(sessdata=SESSDATA, bili_jct=BILI_JCT, buvid3=BUVID3)
    v = video.Video(bvid="BV1AV411x7Gs", credential=credential)

    download_url_data = await v.get_download_url(0)
    detecter = video.VideoDownloadURLDataDetecter(data=download_url_data)
    streams = detecter.detect_best_streams()

    if detecter.check_video_and_audio_stream():
        await download(streams[0].url, "video_temp.m4s", "视频流")
        await download(streams[1].url, "audio_temp.m4s", "音频流")
        os.system(f"{FFMPEG_PATH} -i video_temp.m4s -i audio_temp.m4s -vcodec copy -acodec copy video.mp4")
        os.remove("video_temp.m4s")
        os.remove("audio_temp.m4s")
    else:
        await download(streams[0].url, "flv_temp.flv", "FLV 流")
        os.system(f"{FFMPEG_PATH} -i flv_temp.flv video.mp4")
        os.remove("flv_temp.flv")

    print("已下载为：video.mp4")

asyncio.run(main())
```

## 视频在线人数监测（WebSocket）

```python
import asyncio
from bilibili_api import video

v = video.VideoOnlineMonitor(bvid="BV1AV411x7Gs")

@v.on('ONLINE')
async def on_online_update(event):
    print(f"在线人数: {event}")

@v.on('DANMAKU')
async def on_danmaku(event):
    print(f"弹幕: {event}")

asyncio.get_event_loop().run_until_complete(v.connect())
```

## 获取 AI 视频摘要

```python
import asyncio
from bilibili_api import video

async def main():
    v = video.Video(bvid="BV1xx411c7mD")
    ai_summary = await v.get_ai_conclusion()
    print(ai_summary)

asyncio.run(main())
```
