# npm 模块开发

## 问题

- [x]如何本地调试 npm 包
- [x]如何指定 npm 包包含的文件

## 本地调试

Node规定，使用一个模块时，需要将其安装到全局的或项目的node_modules目录之中。对于开发中的模块，解决方法就是在全局的node_modules目录之中，生成一个符号链接，指向模块的本地目录。

使用分三步走

```sh
// 在开发中的模块目录下 执行 npm link 生成符号链接
src/myModule$ npm link
// 在需要引入模块的项目中执行 npm link，并指定模块名
src/myProject$ npm link myModule
src/myProject$ npm unlink myModule
```

## 指定 npm 模块包含的文件

很多开发文件并不需要一并打入发布的模块中，可以使用这些方式指定包含/忽略的文件：

- package.json 中的 `files` 字段
- 项目的根目录提供一个 `.npmignore` 文件

## 参考

- [npm-package.json](https://docs.npmjs.com/files/package.json)
- [npm模块管理器 npm link](http://javascript.ruanyifeng.com/nodejs/npm.html#toc18)
