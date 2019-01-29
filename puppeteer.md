# 记录puppeteer的使用

## 常用API

- `puppeteer.launch` 启动Chrome
- `browser.newPage` 打开一个新的tab页面
- `page.goto` 页面跳转
- `page.click(selector[, options])` 这里模拟点击传入选择器匹配的元素
- `page.waitForNavigation([options])` 等待页面跳转
- `page.waitForSelector` 等待选择器出现
- `page.waitForResponse` 等待请求返回
- `page.evaluate` 传入函数，执行一段javascript
