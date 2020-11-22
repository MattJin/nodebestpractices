# 使用node命令而不是npm启动容器

## 一段解释

我们经常看到开发者使用`CMD 'npm start'`启动app的代码示例。这是一个不好的做法。`npm`应用不会向您的app转发信号（signals），这将阻止应用优雅关闭（graceful shutdown），（见[/sections/docker/graceful-shutdown.chinese.md]）。如果您使用的是子进程（child-processes），则在意外关闭时无法正确清理它们，将僵尸进程留在主机上。`npm start`也导致无意义的增加一个额外进程。使用`CMD ['node','server.js']`启动您的应用吧。假如您的应用使用了子进程（child-processes），也可以使用`TINI`作为入口。


  We are used to see code examples where folks start their app using `CMD 'npm start'`. This is a bad practice. The `npm` binary will not forward signals to your app which prevents graceful shutdown (see [/sections/docker/graceful-shutdown.md]). If you are using Child-processes they won’t be cleaned up correctly in case of unexpected shutdown, leaving zombie processes on your host. `npm start` also results in having an extra process for no benefit. To start you app use `CMD ['node','server.js']`. If your app spawns child-processes also use `TINI` as an entrypoint.

### 代码示例 - 启动Node

```dockerfile

FROM node:12-slim AS build


WORKDIR /usr/src/app
COPY package.json package-lock.json ./
RUN npm ci --production && npm clean cache --force

CMD ["node", "server.js"]
```


### 代码示例 - 使用Tiny作为入口 Using Tiny as entrypoint

```dockerfile

FROM node:12-slim AS build

# Add Tini if using child-processes
ENV TINI_VERSION v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini

WORKDIR /usr/src/app
COPY package.json package-lock.json ./
RUN npm ci --production && npm clean cache --force

ENTRYPOINT ["/tini", "--"]

CMD ["node", "server.js"]
```

### 反模式

使用npm start
```dockerfile

FROM node:12-slim AS build
WORKDIR /usr/src/app
COPY package.json package-lock.json ./
RUN npm ci --production && npm clean cache --force

# 不要这么做!
CMD "npm start"
```

在同一字符串命令里面使用node将启动一个bash/ash脚本进程去执行您的命令。它和使用`npm`的效果类似。

```dockerfile

FROM node:12-slim AS build
WORKDIR /usr/src/app
COPY package.json package-lock.json ./
RUN npm ci --production && npm clean cache --force

# 不要这么做，它将启动bash
CMD "node server.js"
```

使用npm启动，这里是进程树：
```
$ ps falx
  UID   PID  PPID   COMMAND
    0     1     0   npm
    0    16     1   sh -c node server.js
    0    17    16    \_ node server.js
```
额外的两个进程没有任何好处。

来源:


https://maximorlov.com/process-signals-inside-docker-containers/


https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md#handling-kernel-signals
