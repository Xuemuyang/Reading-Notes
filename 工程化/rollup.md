# rollup

## 疑问

插件的用处

- @rollup/plugin-node-resolve
  - 官方文档说如果使用 babel 就需要装这个插件，照着配置即可
  - 定义是这么说的 The @rollup/plugin-node-resolve plugin teaches Rollup how to find external modules. Install it…
  - 大概意思是会把依赖的文件也给打进来
- @rollup/plugin-commonjs
  - 使用这个模块可以使用 esModule 的方式引入 commonJS 的模块
- rollup 如何支持 typescript
  - 官方文档中提到了使用 @rollup/plugin-typescript 这个插件，参考文档后发现可以直接使用 babel
- 研究一下 babel 相关的 typescript 插件是怎么回事
- rollup 如何使用路径别名
  - 使用一个插件 [rollup-plugin-alias](https://github.com/rollup/rollup-plugin-alias)，在 TS 中修改就会有 lint 报错，此时需要在 tsconfig.json 中加上 baseUrl 和 paths 字段，[tsconfig#paths](https://www.typescriptlang.org/tsconfig#paths)

## 参考

- [rollup 官网](https://rollupjs.org/guide/en/)
- [[译] TypeScript 牵手 Babel：一场美丽的婚姻](https://juejin.im/post/6844903792865984520)
- [Rollup配置(Babel7)](https://xiaogliu.github.io/2019/07/24/rollup-config/)
- [JavaScript模块打包工具Rollup——完全入门指南](https://sosout.github.io/2018/08/04/rollup-tutorial.html)
