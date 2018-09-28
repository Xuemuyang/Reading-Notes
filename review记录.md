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