# 直播操作 - 完整示例

## 获取直播间弹幕和礼物

```python
from bilibili_api import live, sync

room = live.LiveDanmaku(22544798)

@room.on('DANMU_MSG')
async def on_danmaku(event):
    print(event)

@room.on('SEND_GIFT')
async def on_gift(event):
    print(event)

sync(room.connect())
```

## 直播间自动回复弹幕

```python
from bilibili_api import Credential, Danmaku, sync
from bilibili_api.live import LiveDanmaku, LiveRoom

ROOMID = 123
credential = Credential(sessdata="", bili_jct="")
monitor = LiveDanmaku(ROOMID, credential=credential)
sender = LiveRoom(ROOMID, credential=credential)
UID = sync(sender.get_room_info())["room_info"]["uid"]

@monitor.on("DANMU_MSG")
async def recv(event):
    uid = event["data"]["info"][2][0]
    if uid == UID:
        return
    msg = event["data"]["info"][1]
    if msg == "你好":
        await sender.send_danmaku(Danmaku("你好！"))

sync(monitor.connect())
```

## 赠送礼物

```python
from bilibili_api import live, sync, Credential

SESSDATA = ""
BILI_JCT = ""
BUVID3 = ""
self_uid = 0

credential = Credential(sessdata=SESSDATA, bili_jct=BILI_JCT, buvid3=BUVID3)
room = live.LiveRoom(22544798, credential)

gift_config = sync(live.get_gift_config())
for gift in gift_config['list']:
    if gift['name'] == "牛哇牛哇":
        res = sync(room.send_gift_gold(
            uid=self_uid,
            gift_id=gift['id'],
            gift_num=1,
            price=gift['price']
        ))
        print(res)
        break
```
