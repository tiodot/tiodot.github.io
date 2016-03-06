title: 一键收藏网址至Github
date: 2016-03-04 20:18:22
tags: nodejs
---
在网上看见一个用Ruby On Rail写了一个一键收藏网页文章至github上的文章（[点我查看](http://www.jianshu.com/p/19d2f3a3b5d8)）,觉得很想法，于是乎，我也想用nodejs写一个。

### 功能描述
1. chrome扩展，发送当前标签页的标题和网址
2. nodejs后端服务器，获取chrome发送的数据，保存至markdown文件中
3. 定时提交数据至github上

<!-- more -->

### chrome扩展实现
chrome扩展实现：

1. 点击扩展图标时，获取当前标签页的标题和网址，然后将其发送至后端。
2. 映射该功能至标签页面右键菜单，即生成右键菜单的快捷方式。
3. 将发送成功的数据保存至本地，防止多次重复提交。
4. 定时清理保存至浏览器的本地数据，因而chrome的storage是有大小限制的。
5. 在图标上显示badge，来标识是否发送成功或者发送失败。

具体实现见([github源码中markToGithub目录](https://github.com/swxy/bookmarks))。

### nodej后端实现
后端主要实现：

1. 获取扩展发送的网页标题和网址。
2. 保存标题和网址至markdown文件中。
3. 定时提交数据至github上。

为了可以提交数据至github上，首先需要在github上先建一个项目专门用于存放书签，然后将其复制一份到本地，为了省事，直接在该目录下执行后端代码。

在保存书签至markdown文件时，按照每月共用一个文件的方式组织，文件名类似于2016-3.md，然后每个文件中，又按照具体的日期倒序排列收藏。这就要求我们在读取文件时，需要一行一行的读取，以便在正确的位置插入数据。

具体实现见([github源码中的bookmark.js](https://github.com/swxy/bookmarks)), 或者见附录。

### 附录
chrome 扩展的配置文件：
```
{
  "manifest_version": 2,
  "name": "markToGithub",
  "description": "保存书签到github上",
  "version": "1.0",
  "browser_action": {
    "default_icon": "book.png"
  },
  "icons": {
    "16": "book.png",
    "24": "book_24.png"
  },
  "background": {
    "scripts": ["tab.js"],
    "persistent": false
  },
  "permissions": [
    "activeTab",
    "contextMenus",
    "tabs",
    "http://*/",
    "storage"
  ]
}
```


后端代码：
```
"use strict"
const http = require('http');
const url = require('url');
const fs = require('fs');
const readline = require('readline');
const querystring = require('querystring');
const spawnSync = require('child_process').spawnSync;

http.createServer((req, res) => {
    let urlData = url.parse(req.url);
    //console.dir(urlData);
    let queryObj = querystring.parse(urlData.query);
    console.dir(queryObj);
    queryObj.title && queryObj.url && writeToMarKdownDoc(queryObj);
    res.writeHead(200, {"Content-Type": "application/json"});
    res.end(JSON.stringify({
        code: 0,
        msg : 'success'
    }));
}).listen(3175);


function changeFilename () {
    let fileName = 'README.md';
    let name = '';
    let date = new Date();
    if (date.getDate() === 1) {
        name = data.getFullYear() + '_' + data.getMonth() + '.md'
        try {
            let state = fs.state(name);
            !state.isFile() && fs.renameSync(fileName, name);
        }
        catch (e){
            fs.renameSync(fileName, name);
        }
    }
    return fileName;
}

function isNewFile(filename) {
    try {
        let fileState = fs.statSync(filename);
        let result = fileState.isFile();
        return result;
    }
    catch(e) {
        console.error(e);
        return false;
    }
}

function writeToFile(filename, data, title) {
    fs.writeFile(filename, data, (err) => {
        if (err) console.err(err);
        console.log('saved ' + filename);
        //pushToGit(title);
    });
}

function writeToMarKdownDoc(obj) {
    let fileName = changeFilename();
    let data = [];
    let inserted = false;
    const date = new Date();
    if (!isNewFile(fileName)) {
        data.push(`### ${date.getFullYear()}-${date.getMonth()+1}-${date.getDate()}<br />`)
        data.push(`+ [${obj.title}](${obj.url})<br />\n`);
        writeToFile(fileName, data.join('\n'), obj.title);
        return;
    }
    const rl = readline.createInterface({
        input: fs.createReadStream(fileName)
    });

    rl.on('line', (line) => {
        if (!inserted && line.startsWith('###')) {

            let data_str = line.match(/\d{4}-\d{1,2}-\d{1,2}/)[0];
            if (date_str && (new Date(data_str).getDate() === date.getDate())) {
                data.push(line);
                data.push(`+ [${obj.title}](${obj.url})<br />`);
            }
            else {
                data.push(`### ${date.getFullYear()}-${date.getMonth()+1}-${date.getDate()}<br />`)
                data.push(`+ [${obj.title}](${obj.url})<br />\n`);
                data.push(line);
            }
            inserted = true;
        }
        else {
            data.push(line);
        }
    });
    rl.on('close', () => {
        if (!inserted) { //新建的文件
            data.push(`### ${date.getFullYear()}-${date.getMonth()+1}-${date.getDate()}<br />`)
            data.push(`+ [${obj.title}](${obj.url})<br />\n`);
        }
        writeToFile(fileName, data.join('\n'), obj.title);
    });
}

function pushToGit(title) {
    var title = title || '';
    var cmds = [
        ['git', ['add', '.']],
        ['git', ['commit', '-am', `"add ${title}"`]],
        ['git', ['pull', '--rebase']],
        ['git', ['push', 'origin', 'master']]
    ];
    cmds.forEach((cmd) => {
        let result = spawnSync(cmd[0], cmd[1]);
        console.log(result.output.join('\n'));
    });
}

setInterval(function(){
    console.log('push to github');
    pushToGit(new Date().toString());
}, 1000 * 60 * 60 * 2);

console.log('server start at 3175');

```
