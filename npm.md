# npm

## npm 命令含义

```bash
npm init [-f|--force|-y|--yes]
```

这里的`-y`参数是跳过提问阶段

添加参数会生成package.json文件

```bash
npm install
```

简写为`npm i`

参数

+ `--save-dev`是指将包信息添加到`package.json`里的`devDependencies`节点，表示开发时依赖的包。简写为`-D`
+ `--save`是指将包信息添加到`package.json`里的`dependencies`节点，表示发布时依赖的包。

## package.json

`main`字段指定了加载的入口文件。

`dependencies`字段指定了项目运行所依赖的模块，`devDependencies`指定项目开发所需要的模块。

```js
{
  "devDependencies": {
    "browserify": "~13.0.0",
    "karma-browserify": "~5.0.1"
  }
}
```

对应的版本可以加上各种限定，主要有

+ 指定版本，如1.2.2，安装时只安装指定版本
+ 波浪号(tlide) +指定版本，如~1.2.2，表示安装1.2.x的最新版本，但是不安装1.3.x
+ 插入号(caret) +指定版本,如^1.2.2，表示安装1.x.x的最新版本
+ latest 安装最新版本

### `bin`字段

```js
"bin": {
  "someTool": "./bin/someTool.js"
}
```

上面代码指定，`someTool`命令对应的可执行文件为`bin`子目录下的`someTool.js`。Npm会寻找这个文件，在`node_modules/.bin/`目录下建立符号链接。在上面的例子中，someTool.js会建立符号链接`npm_modules/.bin/someTool`。由于`node_modules/.bin/`目录会在运行时加入系统的PATH变量，因此在运行npm时，就可以不带路径，直接通过命令来调用这些脚本。

## nvm

```bash
nvm install node // 安装最新版本node
nvm install 4.2.2 // 指定node版本进行安装
nvm use 4.2.2 // 切换node版本，不加版本切换最新
nvm ls // 列出已安装版本
```