<center><img src="/kabi.png" /></center>

## 介绍

[PieBot2](https://github.com/Kiosoyo/PieBot2) 是一个使用 Python 标准库以及 websockets 库，用于对接 <a href="https://docs.go-cqhttp.org/">go-cqhttp</a> 的开发框架。

目前还在逐渐完善。

## 开始

从 Github clone PieBot2：
```
git clone https://github.com/Kiosoyo/PieBot2.git
```
解压 PieBot ，并 cd 进入 src 目录。

安装基本依赖：
```
pip install -r requirements.txt
```
（创建并）打开 main.py 并在其中键入如下内容：
```python
from bot import bot
from obj import Message


Bot = bot('ws://127.0.0.1:8080/')   # 这个地方的ws链接要和go-cqhttp配置的一样
Bot.run()
```
启动go-cqhttp并配置 正向Websocket 。登陆完成后运行：
```
python main.py
```
链接完成后即可使用。



## 函数注册

PieBot2



## 消息事件(Message)

#### 正则接收

```python
@Bot.reg.register('''\[CQ:at,qq=(.*)\][ ]*''')  # 正则匹配的消息，匹配后会调用
def func_name(message: Message):
    if message_type == 'group':
        Bot.send_group_msg(message.group_id, '怎么了吗？[CQ:at=' + message.user_id + '] ')
        # 或者你也可以使用快速操作，就像下面这样
        # message.reply('怎么了吗？[CQ:at=' + message.user_id + '] ')
        # reply可以多次使用，并且自动判断来源。但只能将消息发送到来源群或私聊
    elif message_type = 'private':
        message.reply('怎么了吗？')
        # Bot.send_private_msg(message.user_id, '怎么了吗？')
```



#### 全部接收

```python
@Bot.reg.registerMessage
def recvMsg(message: Message):			# 这里的 message 和上文是一样的，只不过会收取全部消息，包括群聊和私聊
    print('recv=>', message.message)
```



## 通知事件(Notice)

通知事件有两种格式，一种是 全部接收 一种是 定向接收。



### 全部接收

```python
@Bot.reg.registerNotice()
def func_name(notice: dict):
    print(notice)
    # 这里接受的 notice 字典符合 go-cqhttp 接受到的 json 转换为的字典
```



### 定向接收

```python
@Bot.reg.registerNoticeApp('poke')      # poke 类为戳一戳消息
def func_name(args: dict):              # args 的内容为 go-cqhttp 接受到的 json 转换的字典
    print(f"{args['target_id']}被戳了")
```



## 请求事件(Request)

### 全部接收

```python
@Bot.reg.registerRequest
def recvMsg(request: Request):
    request.reply(True)		# 这个使用了 Request 类的快速操作，可以快速通过或者拒绝请求
    # 或者也可以像下面这样写
    # Bot.set_friend_add_request(request.flag, True)
```



## 自动事件

### 介绍

PieBot2 并非仅能支持事件驱动，同样可以自行编写以实现类似准点报时的功能。



### 编写

```python
@Bot.reg.registerEvent
def eventFunc():
    while True:
        if time.strftime("%H-%M", time.localtime()) == '18-30':
            Bot.send_private_msg(10001, 'hello')
            time.sleep(30)
        time.sleep(30)
```

上面这个例子展示了如何创建一个能在每天下午六点半向 10001 私聊发送 hello 的例子。同样可以使用在群聊等等。