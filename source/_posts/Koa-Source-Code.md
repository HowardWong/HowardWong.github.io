title: Koa Source Code
author: Howard Wong
tags:
  - Node
categories: []
date: 2018-03-01 12:07:00
---
# Intro

```
site: http://koajs.com/
repo: https://github.com/koajs/koa.git
```

---


# Use Case

```javascript
const Koa = require('koa');
const app = new Koa();

// x-response-time

app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  ctx.set('X-Response-Time', `${ms}ms`);
});

// logger

app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}`);
});

// response

app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000);
```

----
- package.json

```
{
  ...
  "main": "lib/application.js",
  ...
}
```

- koa-compose

```javascript

// 处理中间件 返回处理函数
// 调用方式 fn(ctx).then(handleResponse).catch(onerror)

function compose (middleware) {
  return function (context, next) {
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      //下面两行代码
      //如果最后一个中间件还有next 就直接resolve
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve() 
      try {
        // 这里就是传入next执行中间件代码了
        return Promise.resolve(fn(context, function next () {
          return dispatch(i + 1)
        }))
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```

- application.json

```javascript
// Koa构造函数继承自 Emitter 类
module.exports = class Application extends Emitter {

  constructor() {
    super();

    // 默认不信任 proxy header
    this.proxy = false; 
    // app.use注册的中间件
    this.middleware = [];
    // 子域名默认偏移量 默认从右往左第二个点开始截取
    this.subdomainOffset = 2;
    // 环境变量
    this.env = process.env.NODE_ENV || 'development';

    // 创建 context request response 对象
    this.context = Object.create(context);
    this.request = Object.create(request);
    this.response = Object.create(response);
  }

  // 创建http服务 api同http模块
  // http.createServer(this.callback())
  listen(...args) {
    const server = http.createServer(this.callback());
    return server.listen(...args);
  }

  // 注册中间件 将函数push进 this.middleware
  use(fn) {
    this.middleware.push(fn);
    return this;
  }

  // 创建请求处理器
  callback() {
    const fn = compose(this.middleware);

    const handleRequest = (req, res) => {
      res.statusCode = 404;
      const ctx = this.createContext(req, res);
      const onerror = err => ctx.onerror(err);
      const handleResponse = () => respond(ctx);
      onFinished(res, onerror);

      // 将处理好的context 传入 koa-compose生成的函数中
      // 经过koa中间件处理 最终handleResponse处理结果 onerror处理异常
      return fn(ctx).then(handleResponse).catch(onerror);
    };

    return handleRequest;
  }

  // 构建http上下文
  createContext(req, res) {
    const context = Object.create(this.context);
    const request = context.request = Object.create(this.request);
    const response = context.response = Object.create(this.response);
    context.app = request.app = response.app = this;
    context.req = request.req = response.req = req;
    context.res = request.res = response.res = res;
    request.ctx = response.ctx = context;
    request.response = response;
    response.request = request;
    context.originalUrl = request.originalUrl = req.url;
    context.cookies = new Cookies(req, res, {
      keys: this.keys,
      secure: request.secure
    });
    request.ip = request.ips[0] || req.socket.remoteAddress || '';
    context.accept = request.accept = accepts(req);
    context.state = {};
    return context;
  }

  // error时触发
  onerror(err) {
    if (404 == err.status || err.expose) return;
    if (this.silent) return;

    const msg = err.stack || err.toString();
    console.error();
    console.error(msg.replace(/^/gm, '  '));
    console.error();
  }
};
```

# Handle Response

```
ctx.respond 与 ctx.writable 如果为true，不做任何处理（可以用来自行处理response）

根据ctx.body类型处理response报文
- String / Buffer   res.end(body)
- Stream            res.pipe(res)
- Object            res.end(JSON.stringify(body))

设置response Content-Length
ctx.length = Buffer.byteLength(body);
```

```javascript
function respond(ctx) {
  // allow bypassing koa
  if (false === ctx.respond) return;

  const res = ctx.res;
  if (!ctx.writable) return;

  let body = ctx.body;
  const code = ctx.status;

  // ignore body
  if (statuses.empty[code]) {
    // strip headers
    ctx.body = null;
    return res.end();
  }

  if ('HEAD' == ctx.method) {
    if (!res.headersSent && isJSON(body)) {
      ctx.length = Buffer.byteLength(JSON.stringify(body));
    }
    return res.end();
  }

  // status body
  if (null == body) {
    body = ctx.message || String(code);
    if (!res.headersSent) {
      ctx.type = 'text';
      ctx.length = Buffer.byteLength(body);
    }
    return res.end(body);
  }

  // responses
  if (Buffer.isBuffer(body)) return res.end(body);
  if ('string' == typeof body) return res.end(body);
  if (body instanceof Stream) return body.pipe(res);

  // body: json
  body = JSON.stringify(body);
  if (!res.headersSent) {
    ctx.length = Buffer.byteLength(body);
  }
  res.end(body);
}
```


# Context Structure
![upload successful](/images/pasted-0.png)