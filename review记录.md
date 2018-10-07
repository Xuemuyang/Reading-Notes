# review

记录别人对我的review

## 记录

1.与环境相关代码不要提交

2.组件化开发中，当传入的prop为布尔值的时候，值为`true`可以忽略

3.数组的api灵活使用

```js
formatList(arr) {
  const _arr = [];
  arr.forEach(item => {
    item.checked && _arr.push(item.way);
  });
  return _arr.join(' ');
}
```

优化为

```js
formatList(arr) {
  return arr.filter(({ checked }) => checked).map(({way}) => way).join(' ');
}
```

4.表单按照场景来确定使用什么值,`0,1,2`或者直接用`男/女`，某些场景下可以少一步映射操作

5.一段用于字符串拼接的代码

```js
let str = '信息'

const infoMap = {
  name: '名称',
  age: '年龄'
};

const data = {
  name: 'myoungxue',
  age: 22
}

// 其他字段
str += ['name', 'age']
  .filter(key => !!data[key])
  .map(key => `\n${infoMap[key]}：${data[key]}`)
  .join('，')
```

```console
"信息
名称：myoungxue，
年龄：22"
```