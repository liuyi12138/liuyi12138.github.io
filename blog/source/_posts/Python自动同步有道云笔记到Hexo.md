---
title: Python自动同步有道云笔记到Hexo
date: 2019-03-21 16:59:16
tags: [Python,轮子]
categories: 编程语言
---


**对一个[大佬的代码](https://github.com/liuyi12138/ynote2hexo)进行了删改，不过貌似大佬并没有理我的gayhub合并请求**

<!--more-->

* 2019.4.15   今天发现我的分支被人fork了！有点开心，感觉自己为开源社区做出来一丢丢贡献这样子~~~
* 2019.4.16   发现cookie过期了，感觉需要写一下登录了
* 2019.4.16   查了一下有道云的官方api好像已经没有人维护了，所以只能使用现有的api，因此还是要从cookie的值来解决问题。起初想到的是直接从浏览器获取cookie，但代码在云端运行就拿不到cookie了，又想到模拟登陆后获取cookie，但一方面我的账号是qq登录，另一方面有道云对密码做了加密所以没法post数据过去，暂时没想到有什么好的方法可以解决。
* 2019.4.18  尝试构建post表单来qq登录获取cookie，但有道云的页面显然是做过加密的，获取并不容易。现在有两个思路:
    * 1.服务端使用一个文件读取cookie，并且构建一个udp通信，本地读取cookie后同步到服务端文件。但依旧绕不开获取cookie，本地可以尝试在Chrome的cookie文件获取cookie
    * 2.申请有道云api的秘钥，使用其api来进行文件获取。但不知道能不能拿到秘钥以及工程量比较大
    * 总而言之就是还没有一个很好的方法来解决问题，继续尝试中。。。




```python
#coding=utf-8
import sys
import requests
import time
import re
import os
import shutil
import hashlib
import commands
import Queue

import threading

reload(sys)
sys.setdefaultencoding('utf8')

# Hexo 文件夹
HEXO_DIR = '/blog/source/_posts'

# 登录有道云笔记后存储在 Cookie 里的值
YNOTE_PERS = 'v2|cqq||YNOTE||web||7776000000||1553092933721||115.156.142.129||qq7265F91BB452AB2B63C3B07B55887AEB||eShHUMhLTZ0l5OMp4646z0JL6LPynMTy0QZOLkGnMe40JFOLqSh4JyROG0Mw4h4zA0zYOMgZ6Lg4RgFPLpBP46uR'
YNOTE_SESS = 'v2|3H29AJgmLRgykfkERMOY06FhHUGRMqL0lGnMQuPLwFRYm6MTF0LJuRzfhLQ4P4zW0z50MpKOfzY0q4OfUGO4pz0pBkLU5RHJLR'
YNOTE_LOGIN = '5||1553093006110'
CSTK = 'qd9PNsay'

HEADERS = {
    'Accept-Encoding':
    'gzip, deflate, br',
    'User-Agent':
    'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36',
    'Cookie':
    'YNOTE_PERS={YNOTE_PERS}; YNOTE_SESS={YNOTE_SESS}; YNOTE_LOGIN={YNOTE_LOGIN}; YNOTE_CSTK={YNOTE_CSTK}'.
    format(
        YNOTE_PERS=YNOTE_PERS,
        YNOTE_SESS=YNOTE_SESS,
        YNOTE_LOGIN=YNOTE_LOGIN,
        YNOTE_CSTK=CSTK),
    'Accept':
    'application/json, text/plain, */*',
    'Host':
    'note.youdao.com',
    'Origin':
    'https://note.youdao.com',
    'Referer':
    'https://note.youdao.com/web/',
    'Content-Type':
    'application/x-www-form-urlencoded;charset=UTF-8'
}



# 获取某笔记本下所有的笔记本
def getBooks(path):
    data = {'path': path, 'dirOnly': True, 'f': True, 'cstk': CSTK}
    url = 'https://note.youdao.com/yws/api/personal/file?method=listEntireByParentPath&cstk={CSTK}&keyfrom=web'.format(
        CSTK=CSTK)
    res = requests.post(url, data=data, headers=HEADERS)
    if res.status_code == 200:
        resJson = res.json()
        books = []
        for i in resJson:
            # _私密, _开头的笔记本认为是私密笔记, 跳过
            if i['fileEntry']['name'][0] != '_':
                books.append({
                    'name': i['fileEntry']['name'],
                    'id': i['fileEntry']['id']
                })
        print(len(books))
        return books
    else:
        exit('getBooks')



# 获取所有的笔记本
def getAllBooks():
    ids = Queue.Queue()
    ids.put('/')
    books = []
    while not ids.empty():
        print(ids.queue)
        tempId = ids.get()
        tempBooks = getBooks(tempId)
        for book in tempBooks:
            if tempId == '/':
                aId = tempId + book['id']
            else:
                aId = tempId + '/' + book['id']
            ids.put(aId)
            books.append({
                'name': book['name'],
                'id': book['id']
            })
    print ('allbooks')
    print(len(books))
    return books


# 获取笔记本下的笔记
def getAllNotes(book):
    url = 'https://note.youdao.com/yws/api/personal/file/{id}?all=true&cstk={CSTK}&f=true&isReverse=false&keyfrom=web&len=30&method=listPageByParentId&sort=1'.format(
        id=book['id'], CSTK=CSTK)
    res = requests.get(url, headers=HEADERS)
    if res.status_code == 200:
        resJson = res.json()
        notes = []
        for i in resJson['entries']:

            # 选出后缀名为md的文件
            if i['fileEntry']['name'][-2:] == 'md' and i['fileEntry']['name'][0] != '_':
                notes.append({
                    'name':
                    i['fileEntry']['name'],
                    'id':
                    i['fileEntry']['id'],
                    'createTime':
                    i['fileEntry']['createTimeForSort'],
                    'modifyTime':
                    i['fileEntry']['modifyTimeForSort'],
                    'tag':
                    book['name']
                })
        return notes
    else:
        exit('getAllNotes')


# 根据笔记信息获取笔记内容
def getNoteDetail(note):
    url = 'https://note.youdao.com/yws/api/personal/file/{id}?method=download&read=true&cstk={CSTK}'.format(
        id=note['id'], CSTK=CSTK)
    res = requests.get(url, headers=HEADERS)
    if res.status_code:
        resCon = res.content

        time = ''
        if note['modifyTime']:  # 优先选用修改时间
            time = parseTS(note['modifyTime'])
        else:
            time = parseTS(note['createTime'])

        detail = {
            'name': filterMark(note['name']),
            'time': time,
            'content': resCon,
            'tag': note['tag']
        }
        return detail
    else:
        exit('getNoteDetail')


# 写入文档
def writeMd(detail):
    print('写入: {name}'.format(name=detail['name']))
    with open('_posts/' + detail['name'], 'w') as f:
        # f.write('---\n')
        # f.write('title: {title}\n'.format(title=detail['name'][:-3]))
        # f.write('date: {data}\n'.format(data=detail['time']))
        # f.write('tags: {tag}\n'.format(tag=detail['tag']))
        # f.write('---\n\n\n')
        f.write(detail['content'])
        f.write('\n')


# 将 _posts 目录替换到 hexo/source/_posts, 并部署提交
# 注意: 这将删除原有 _posts 目录, 请事先备份
def deployHexo():
    shutil.rmtree(HEXO_DIR)
    shutil.copytree('_posts', HEXO_DIR)
    print('开始部署 hexo ...')
    dep = commands.getstatusoutput(
        'cd {hexo_dir} && hexo clean && hexo g && hexo d'.format(
            hexo_dir=HEXO_DIR))
    if dep[0] == 0:
        print('部署成功')
    else:
        print('部署失败')
        print(dep)


# 将10位时间戳转为 2017-06-29 10:00:00 的格式
def parseTS(ts):
    timeArr = time.localtime(ts)
    return time.strftime("%Y-%m-%d %H:%M:%S", timeArr)


# 过滤特殊字符, 移除原有后缀后重新添加.md
def filterMark(s):
    # s = s.decode("utf8")
    res = re.sub(
        "[\s+\.\!\/_,$%^*(+\"\']+|[+——！，。？、~@#￥%……&*（）()]+".decode("utf8"),
        "".decode("utf8"), s)
    res = res.replace(' ', '')
    return res[:-2] + '.md'


# MD5 加密
def md5(str):
    md5 = hashlib.md5()
    md5.update(str)
    return md5.hexdigest()


# 退出程序
def exit(why):
    print('{why} 出错了'.format(why=why))
    os._exit(0)


# 入口
def start():
    if os.path.exists('_posts'):
        shutil.rmtree(r'_posts')
    os.mkdir(r'_posts')
    books = getAllBooks()
    for i in books:
        notes = getAllNotes(i)
        print(len(notes))
        for j in notes:
            detail = getNoteDetail(j)
            writeMd(detail)
    deployHexo()

    t = threading.Timer(432000, start) 
    t.start() 

if __name__ == '__main__':
    start()
    # time.sleep(24 * 60 * 60)
```

* 使用nohup python youdaoToHexo.py &运行代码 (守护进程)
* [nohup和&后台运行，进程查看及终止](https://www.cnblogs.com/baby123/p/6477429.html)
* 根目录下的文件以及以"_"开头的文件不会被copy
* ps -aux|grep youdaoToHexo.py 查看运行状态
