# umi

## 疑问

- what
  - umi 官方的介绍中与 create-react-app 做了对比，可以理解为一个应用框架
- why
- how

## 记录

- 约定大于配置，但是向现实低头，路由支持配置式

### [配置](https://umijs.org/zh-CN/docs/config)

Umi 在 `.umirc.ts` 或 `config/config.ts` 中进行项目的配置

`defineConfig` 这个写法是为了在写配置文件的时候能有代码提示

### [路由](https://umijs.org/zh-CN/docs/routing)

#### 子路由渲染

```js
export default {
  routes: [
    { path: '/login', component: 'login' },
    {
      path: '/',
      component: '@/layouts/index',
      routes: [
        { path: '/list', component: 'list' },
        { path: '/admin', component: 'admin' },
      ],
    },
  ],
}
```

在 `src/layouts/index` 中通过 `props.children` 渲染子路由

```js
export default (props) => {
  return <div style={{ padding: 20 }}>{ props.children }</div>;
}
```

访问 `/list` 和 `/admin` 就会带上 `src/layouts/index` 这个 layout 组件

#### wrappers

```js
export default {
  routes: [
    { path: '/user', component: 'user',
      wrappers: [
        '@/wrappers/auth',
      ],
    },
    { path: '/login', component: 'login' },
  ]
}
```

在 `src/wrappers/auth` 中

```js
import { Redirect } from 'umi'
export default (props) => {
  const { isLogin } = useAuth();
  if (isLogin) {
    return <div>{ props.children }</div>;
  } else {
    return <Redirect to="/login" />;
  }
}
```

访问 `/user`，就通过 `useAuth` 做权限校验，如果通过，渲染 `src/pages/user`，否则跳转到 `/login`，由 `src/pages/login` 进行渲染

#### [约定式路由](https://umijs.org/zh-CN/docs/convention-routing)

与 egg 类似

## 参考资料

- [UMI 官方网站](https://umijs.org/zh-CN/docs)
