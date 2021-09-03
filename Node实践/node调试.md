# Node 调试

## VScode 配置

调试单个文件

```json
{
  // 使用 IntelliSense 了解相关属性。
  // 悬停以查看现有属性的描述。
  // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "启动程序",
      "program": "${file}"
    }
  ]
}
```

突然某一天就出现了找不到路径的错误

终端使用 `which node` 命令找到 Node 的路径，之后添加到 "runtimeExecutable": "/Users/mac/.nvm/versions/node/v14.17.3/bin/node" 到 configurations 中。
