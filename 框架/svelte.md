## Svelte

## 疑问

## 记录

- Shorthand attributes(速记属性)

当属性的 name 和 value 一样的时候，有一个简写方式

```html
<img {src} alt="A man dances.">
```

- 渲染 HTML

```html
<script>
  const string = `this string contains some <strong>HTML!!!</strong>`;
</script>

<p>{@html string}</p>
```

- 计算属性

```js
let count = 0;
$: doubled = count * 2;
```

- 响应式(类比 watch 和 computed)

```js
$: console.log(`the count is ${count}`);
```

```js
$: {
  console.log(`the count is ${count}`);
  alert(`I SAID THE COUNT IS ${count}`);
}
```

```js
$: if (count >= 10) {
  alert(`count is dangerously high!`);
  count = 9;
}
```

- Props 属性传递
  - 设置默认值
  - 对象方式展开传递

```html
// Nested.svelte
<script>
  // 设置默认值 12
  export let answer = '12';
</script>

<p>The answer is {answer}</p>
```

```html
// App.svelte
<script>
  import Nested from './Nested.svelte';
</script>

<Nested answer={42}/>
```

spread 运算符传递属性

```html
// App.svelte
<script>
  import Info from './Info.svelte';

  const pkg = {
    name: 'svelte',
    version: 3,
    speed: 'blazing',
    website: 'https://svelte.dev'
  };
</script>

<Info name={pkg.name} version={pkg.version} speed={pkg.speed} website={pkg.website}/>
```

```html
// Info.svelte
<script>
  export let name;
  export let version;
  export let speed;
  export let website;
</script>

<p>
  The <code>{name}</code> package is {speed} fast.
  Download version {version} from <a href="https://www.npmjs.com/package/{name}">npm</a>
  and <a href={website}>learn more here</a>
</p>
```

- 模板判断
  - 不支持 else-if

```html
<script>
  let x = 7;
</script>

{#if x > 10}
  <p>{x} is greater than 10</p>
  {:else}
    {#if 5 > x}
      <p>{x} is less than 5</p>
    {:else}
      <p>{x} is between 5 and 10</p>
  {/if}
{/if}
```

- 模板循环

```html
<script>
let cats = [
  { id: 'J---aiyznGQ', name: 'Keyboard Cat' },
  { id: 'z_AbfPXTKms', name: 'Maru' },
  { id: 'OUtn3pvWmpg', name: 'Henri The Existential Cat' }
];
</script>

{#each cats as cat, i}
  <li><a target="_blank" href="https://www.youtube.com/watch?v={cat.id}">
    {i + 1}: {cat.name}
  </a></li>
{/each}
```

解构写法

```html
{#each cats as {name, id}, i}
  <li><a target="_blank" href="https://www.youtube.com/watch?v={cat.id}">
    {i + 1}: {name}
  </a></li>
{/each}
```

- 模板中的异步写法

```html
<script>
  async function getRandomNumber() {
    const res = await fetch(`tutorial/random-number`);
    const text = await res.text();

    if (res.ok) {
      return text;
    } else {
      throw new Error(text);
    }
  }

  let promise = getRandomNumber();

  function handleClick() {
    promise = getRandomNumber();
  }
</script>

<button on:click={handleClick}>
  generate random number
</button>

<!-- replace this element -->
{#await promise}
  <p>...waiting</p>
{:then number}
  <p>The number is {number}</p>
{:catch error}
  <p style="color: red">{error.message}</p>
{/await}
```

- 事件绑定

```html
<div on:mousemove={handleMousemove}>
  The mouse position is {m.x} x {m.y}
</div>
```

- 事件修饰符
  - preventDefault
  - stopPropagation
  - passive
  - nonpassive
  - capture
  - once
  - self

```html
<button on:click|once={handleClick}>
  Click me
</button>
```

- 组件派发事件

```html
// App.svelte
<script>
  import Inner from './Inner.svelte';

  function handleMessage(event) {
    alert(event.detail.text);
  }
</script>

<Inner on:message={handleMessage}/>
```

```html
// Inner.svelte
<script>
import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  function sayHello() {
    dispatch('message', {
      text: 'Hello!'
    });
  }
</script>

<button on:click={sayHello}>
  Click to say hello
</button>
```

- 数据绑定

```html
<script>
  let name = 'world';
</script>

<input bind:value={name}>

<h1>Hello {name}!</h1>
```

通过 bind:this 获取引用

```html
<canvas
  bind:this={canvas}
  width={32}
  height={32}
></canvas>
```

- 生命周期
  - onMount
  - onDestroy
  - beforeUpdate
  - afterUpdate
  - tick

tick 是一个特殊的钩子，文档是这样解释的，在数据改变后 Svelte 内部不会立马去改变 dom，而是会在一个 microtask 一起操作，可以使用 await tick() 等到拿到 dom 变更完成的异步时机，执行其他操作，比如官方的例子是操作 textarea，使用 tab 标签变更大小写 [Lifecycle/tick](https://svelte.dev/tutorial/tick)

## 参考

- [Svelte-Github](https://github.com/sveltejs/svelte)
- [Svelte 官网](https://svelte.dev/)
- [Svelte 中文官网](https://www.sveltejs.cn/)
