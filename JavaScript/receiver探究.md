# receiver 探究

## 参考

- [Proxy 是代理，Reflect 是干嘛用的？](https://www.zhangxinxu.com/wordpress/2021/07/js-proxy-reflect/)
- [为什么Proxy一定要配合Reflect使用？](https://juejin.cn/post/7080916820353351688)
- [Reflect.set](https://es6.ruanyifeng.com/#docs/reflect#Reflect-settarget-name-value-receiver)

## 探究

当出现了继承的时候，为了明确调用主体（主要是修正 `this`），就需要 receiver 参数，直接看张鑫旭文章中的例子。

```js
let miaoMiao = {
  _name: '疫苗',
  get name () {
    return this._name;
  }
}
let miaoXy = new Proxy(miaoMiao, {
  get (target, prop, receiver) {
    return target[prop];
  }
});

let kexingMiao = {
  __proto__: miaoXy,
  _name: '科兴疫苗'
};

// 结果是疫苗
console.log(kexingMiao.name);
```

```js
let miaoMiao = {
  _name: '疫苗',
  get name () {
    return this._name;
  }
}
let miaoXy = new Proxy(miaoMiao, {
  get (target, prop, receiver) {
    return Reflect.get(target, prop, receiver);
    // 也可以简写为 Reflect.get(...arguments)
  }
});

let kexingMiao = {
  __proto__: miaoXy,
  _name: '科兴疫苗'
};

// 结果是科兴疫苗
console.log(kexingMiao.name);
```

`receiver` 是为了传递正确的调用者。

还有一个需要注意的点是，proxy 配置中的 `this` 指向 `handler` 对象。

```js
const parent = {
  get value() {
    return '19Qingfeng';
  },
};

const handler = {
  get(target, key, receiver) {
    console.log(this === handler); // log: true
    console.log(receiver === obj); // log: true
    return target[key];
  },
};

const proxy = new Proxy(parent, handler);

const obj = {
  name: 'wang.haoyu',
};

// 设置obj继承与parent的代理对象proxy
Object.setPrototypeOf(obj, proxy);

// log: false
obj.value
```
