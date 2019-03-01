# nest

## 问题

- RXjs Observer

## Overview

### Controllers

Controllers 接收 request，并响应 response

cats.controller.ts

```ts
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll() {
    return 'This action returns all cats';
  }
}
```

`@Get()` 会让所有的 `/cats` GET 请求进入这个方法
