# lerna

> Lerna 是一个用来优化托管在 git/npm 上的多 package 代码库的工作流的一个管理工具，可以让你在主项目下管理多个子项目，从而解决了多个包互相依赖，且发布时需要手动维护多个包的问题。

## 概念

- monorepo
- multirepo

## 疑问

- 两个包相互依赖，如何 debug
  - 使用 lerna link
- 项目如何启动
- lerna.json 中的配置
- Fixed 和 Independent 管理方式
  - 大概意思就是版本号的控制，Fixed 模式下版本号在 `lerna.json` 中统一控制，Independent 模式中在发布的时候对每个更改的包版本号进行独立的控制，Independent 模式更加的自定义化
- postinstall 钩子

## cheatsheet

`lerna bootstrap --hoist`

将依赖包提升到工程根目录

`lerna run <script>`

在每一个 package 中运行对应的 npm scripts

`lerna link` 命令可以自动 link 模块依赖

## 参考

- [lerna-Github](https://github.com/lerna/lerna)
- [lerna管理前端packages的最佳实践](https://juejin.im/post/5a989fb451882555731b88c2)
- [lerna+yarn workspace+monorepo项目的最佳实践](https://juejin.im/post/5d583231e51d45620541039e)
- [Lerna 中文教程详解](https://juejin.im/post/5ced1609e51d455d850d3a6c)
- [REPO 风格之争：MONO VS MULTI](https://zhuanlan.zhihu.com/p/31289463)
