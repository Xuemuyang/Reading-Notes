# egg

## 问题

- 约定优于配置该如何理解
  - config.plugin 是什么东西
  - Egg 在 Koa 基础上进入了约定
- npm init egg 为什么可以脚手架
- [x] Helper 类在哪 API 文档中可以找到，在 extend 中进行扩展
- appveyor.yml 是什么文件
- egg 下面的包都是干什么的
  - egg-bin
  - egg-scripts
  - egg-ci
  - egg-mock
  - egg-sequelize
    - 领域模型
- 如何生成 API 文档
- benchmark 基准测试，对性能的衡量
- 多套环境如何配置
- 配置之间的覆盖关系

## 特点

- 基于 Koa
- 定制性高，提供插件机制，一个插件只做一件事
- 约定优于配置

## 基础功能

### 目录结构

```tree
egg-project
├── package.json
├── app.js (可选) // 自定义启动时的初始化工作
├── agent.js (可选) // 自定义启动时的初始化工作 agent 机制
├── app
|   ├── router.js  // 配置 URL 路由规则
│   ├── controller // 解析用户的输入，处理后返回相应的结果
│   |   └── home.js
│   ├── service (可选) // 用于编写业务逻辑
│   |   └── user.js
│   ├── middleware (可选) // 用于编写中间件
│   |   └── response_time.js
│   ├── schedule (可选) // 用于定时任务
│   |   └── my_task.js
│   ├── public (可选) // 用于放置静态资源
│   |   └── reset.css
│   ├── view (可选) // 用于放置模板文件
│   |   └── home.tpl
│   ├── model (可选) // 用于放置领域模型
│   |   └── model.js
│   └── extend (可选) // 用于框架的扩展
│       ├── helper.js (可选)
│       ├── request.js (可选)
│       ├── response.js (可选)
│       ├── context.js (可选)
│       ├── application.js (可选)
│       └── agent.js (可选)
├── config // 用于编写配置文件
|   ├── plugin.js
|   ├── config.default.js
│   ├── config.prod.js
|   ├── config.test.js (可选)
|   ├── config.local.js (可选)
|   └── config.unittest.js (可选)
└── test // 用于单元测试
    ├── middleware
    |   └── response_time.test.js
    └── controller
        └── home.test.js
```

自定义目录规范，参考 Loader

### 框架内置基础对象

- Koa 继承
  - Application
  - Context
  - Request
  - Response
- 框架扩展
  - Controller
  - Service
  - Helper
  - Config
  - Logger

弄清各个对象的概念(是什么)，关系(继承)，访问方式

#### Application

- 概念
  - 全局应用对象，一个应用中，只会实例化一个
  - 继承自 Koa.Application
- 访问方式
  - 在 Context 对象上，通过 `ctx.app` 访问
  - 在 Controller、Service 基类的实例中，通过 `this.app` 访问

#### Context

- 概念
  - 一个请求级别的对象，**每收到用户请求时，初始化 Context 对象**，这个对象封装了这次用户请求的信息，并提供了许多便捷的方法来获取请求参数或者设置响应信息，框架会将 Service 挂载到 Context 上
  - 继承自 Koa.context
- 获取方式
  - 在 Controller、Service 中，`this.ctx` 中访问
  - Middleware 则是作为第一个参数传入，🌰
  - 在非请求场景下访问 Context 实例上的对象，可以通过 `Application.createAnonymousContext()` 创建匿名 Context 实例 🌰
  - 定时任务中每一个 task 都接受一个 Context 实例作为参数 🌰

```js
// Middleware 中获取 Context 对象
async function middleware(ctx, next) {
  // ctx is instance of Context
  console.log(ctx.query);
}
```

```js
// 创建匿名 Context
// app.js
module.exports = app => {
  app.beforeStart(async () => {
    const ctx = app.createAnonymousContext();
    // preload before app start
    await ctx.service.posts.load();
  });
}
```

```js
// 定时任务
// app/schedule/refresh.js
exports.task = async ctx => {
  await ctx.service.posts.refresh();
};
```

#### Request & Response

- 概念
  - 请求级别的对象，继承自 Koa.Request/Koa.Resonse，对 Node.js 原生 HTTP Request/Response 对象进行封装，提供了一系列辅助方法获取 HTTP 请求常用参数
- 获取方式
  - `ctx.request` 和 `ctx.response`
  - Koa 会在 Context 上代理一部分 Request 和 Response 上的方法和属性，参考 [Koa Context Request aliases](https://koajs.com/#context)

#### Controller & Service

- 概念
  - 框架提供了一个 Controller/Service 基类，推荐所有的 Controller/Service 都继承该基类实现，有如下属性
- 属性
  - ctx 当前请求的 Context 实例
  - app 应用的 Application 实例
  - config 应用的配置
  - service 应用所有的 Service
  - logger

#### Helper

- 概念
  - Helper 用来提供一些实用的 utility 函数
  - Helper 是一个类，有和 Controller 基类一样的属性，也会在每次请求时进行实例化
- 获取方式
  - `ctx.helper`
- 自带方法
  - [API文档-helper](https://eggjs.org/api/Helper.html#main)
  - egg 本身集成插件，插件中的 helper 可以使用
- 自定义 helper 方法 🌰

```js
// 自定义 helper 方法
// app/extend/helper.js
module.exports = {
  formatUser(user) {
    return only(user, [ 'name', 'phone' ]);
  }
};
```

#### Config

- 概念
  - 框架的配置
- 获取方式
  - `app.config`
  - 在 Controller、Service、Helper 的实例上通过 `this.config` 访问

#### Logger

// TODO:

- 概念
  - egg 内置了非常多的 log，适用于不同场景，可以展开慢慢研究

### 运行环境 && 配置

应用内通过 `app.config.env` 进行访问

框架的配置会进行合并

### 中间件

- 使用
  - 在应用中使用
    - `config.default.js` 中配置即可
  - 在 router 中使用，可以针对单个 router 生效
- 通用配置
  - `enable`
  - `match`
  - `ignore`

### Router

// TODO: 404 如何配置

- 概念
  - Router 主要用来描述请求 URL 和具体承担执行动作的 Controller 的对应关系， 框架约定了 `app/router.js` 文件用于统一所有路由规则。
- 路由定义
  - `router.verb('router-name', 'path-match', middleware1, ..., middlewareN, app.controller.action);`
    - `verb` 用户触发动作，支持 HTTP 全部方法
    - `router-name` 是别名
    - `path-match` 是路由 URL 路径
    - middleware 可以配置多个
    - controller 可以有两种写法，`app.controller.user.fetch` 指定具体的 controller，或者字符串 `'user.fetch'`
    - 对别名和 URL 路径有疑问看下面的重定向 🌰 就理解了
  - `router.redirect` 是重定向方法
  - `router.resources` 方法可以再一个路径上快速生成 CRUD 路由结构
- router 实战 以下参考官方文档均能找到答案
  - 参数获取
  - 表单内容获取
  - 表单校验
  - 重定向
    - 内部重定向
    - 外部重定向
  - 中间件的使用
  - 路由目录结构梳理

```js
// 内部重定向
// app/router.js
module.exports = app => {
  app.router.get('index', '/home/index', app.controller.home.index);
  app.router.redirect('/', '/home/index', 302);
};

// 外部重定向
// app/router.js
module.exports = app => {
  app.router.get('/search', app.controller.search.index);
};

// app/controller/search.js
exports.index = async ctx => {
  const type = ctx.query.type;
  const q = ctx.query.q || 'nodejs';

  if (type === 'bing') {
    ctx.redirect(`http://cn.bing.com/search?q=${q}`);
  } else {
    ctx.redirect(`https://www.google.co.kr/search?q=${q}`);
  }
};
```

### Controller

- 概念
  - Controller 层主要对用户的请求参数进行处理（校验、转换），然后调用对应的 Service 方法处理业务，得到业务结果后封装并返回
    - 获取用户通过 HTTP 传递过来的请求参数
    - 校验、组装参数
    - 调用 Service 进行业务处理，必要时处理转换 Service 的返回结果，让它适应用户的需求
    - 通过 HTTP 将结果响应给用户
- 使用
  - 定义 Controller 类，继承自 `egg.Controller`，会有如下属性挂在 `this` 上
    - `this.ctx`
    - `this.app`
    - `this.service`
    - `this.config`
    - `this.logger`
  - 自定义 Controller 基类 🌰

```js
// 自定义 Controller 基类
// app/core/base_controller.js
const { Controller } = require('egg');
class BaseController extends Controller {
  get user() {
    return this.ctx.session.user;
  }

  success(data) {
    this.ctx.body = {
      success: true,
      data,
    };
  }

  notFound(msg) {
    msg = msg || 'not found';
    this.ctx.throw(404, msg);
  }
}
module.exports = BaseController;
```

### Service

- 概念
  - Service 就是在复杂业务场景下用于做业务逻辑封装的一个抽象层，有以下几个好处：
    - 保持 Controller 中的逻辑更加简洁
    - 保持业务逻辑的独立性，抽象出来的 Service 可以被多个 Controller 重复调用
    - 将逻辑和展现分离，更容易编写测试用例

### 插件

- 概念
  - 弄清楚中间件、插件、应用的关系
    - 中间件的弊端
      - 中间件加载有顺序
      - 中间件的定位更多是与用户请求相关，如鉴权、安全检查、访问日志，洋葱模型的业务，有些功能不符合这种业务形态，如定时任务、消息订阅
      - 某些功能的初始化逻辑比较复杂，需要在应用启动时完成
    - 插件
      - 管理编排相对独立的业务逻辑
      - 理解为一个迷你的应用，没有 Router 和 Controller，且只能声明跟其他插件的依赖
      - 多个插件可以包装为一个上层框架
- 使用
  - 插件在应用/框架的 `config/plugin.js` 中声明 🌰
  - 对于还没有抽成独立 npm 包的插件，可以使用 path 引入，具体参考 [渐进式开发](https://eggjs.org/zh-cn/tutorials/progressive.html)
- 自带插件
  - egg 本身默认内置了很多[常用插件](https://eggjs.org/zh-cn/basics/plugin.html#%E6%8F%92%E4%BB%B6%E5%88%97%E8%A1%A8)

```js
// config/plugin.js
// 使用 mysql 插件
exports.mysql = {
  enable: true,
  package: 'egg-mysql',
};

// 在 app 上访问插件
app.mysql.query(sql, values);
```

### 框架扩展

- 概念
  - 用来扩展框架自身的功能，加强框架的功能
    - Application
    - Context
    - Request
    - Response
    - Helper
  - 如果考虑到复用，功能较为独立，可以加入 plugin

## 参考

- [egg](https://eggjs.org/zh-cn/)
- [Koa](https://koajs.com/)
