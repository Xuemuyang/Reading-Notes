# Node.js API

## fs

使用 [`fs-extra`](https://github.com/jprichardson/node-fs-extra/blob/master/README.md#methods) 来代替 fs 模块。

- copy 复制文件或目录
- emptyDir 确保目录为空，不为空则删除，不存在创建
- ensureFile 确保文件存在，存在不做修改，不存在创建
- ensureDir 确保目录存在，不存在则创建
- ensureLink 确保链接存在，不存在则创建
- ensureSymlink 确保软链接存在，不存在则创建
- move 移动文件或目录
- outputFile 与 `writeFile` 很像，区别是如果父目录不存在，则会创建
- outputJson 与 `writeJson` 很像，区别是如果父目录不存在，则会创建
- pathExists 检查目标文件在文件系统中是否存在
- readJson 读一个 JSON 文件
- remove 删除文件或目录，类似 `rm -rf`
- writeJson 将一个对象写入 JSON 文件

```js
const fs = require('fs-extra')

// Async with promises:
fs.copy('/tmp/myfile', '/tmp/mynewfile')
  .then(() => console.log('success!'))
  .catch(err => console.error(err))

// Async with callbacks:
fs.copy('/tmp/myfile', '/tmp/mynewfile', err => {
  if (err) return console.error(err)
  console.log('success!')
})

// Sync:
try {
  fs.copySync('/tmp/myfile', '/tmp/mynewfile')
  console.log('success!')
} catch (err) {
  console.error(err)
}

// Async/Await:
async function copyFiles () {
  try {
    await fs.copy('/tmp/myfile', '/tmp/mynewfile')
    console.log('success!')
  } catch (err) {
    console.error(err)
  }
}

copyFiles()
```
