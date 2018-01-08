# npm

## npm 命令含义

```bash
npm init [-f|--force|-y|--yes]
```

添加参数会生成package.json文件

```bash
npm install
```

简写为`npm i`

参数

+ `--save-dev`是指将包信息添加到`package.json`里的`devDependencies`节点，表示开发时依赖的包。简写为`-D`
+ `--save`是指将包信息添加到`package.json`里的`dependencies`节点，表示发布时依赖的包。

## nvm

```bash
nvm install node // 安装最新版本node
nvm install 4.2.2 // 指定node版本进行安装
nvm use 4.2.2 // 切换node版本，不加版本切换最新
nvm ls // 列出已安装版本
```