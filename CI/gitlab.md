# Gitlab

## 疑问

- [ ] runner 执行 jobs 的时候，是在环境下执行
- [ ] 如何在本地调试 CI 命令

## 核心概念以及之间关系

### 核心概念

- `.gitlab-ci.yml`
- `runner` (run job)
- `pipeline`
  - `jobs`
  - `stage`

Gitlab 提供了 CI(Continuous Integration) 持续集成的服务，触发这个服务需要有两个条件

- 项目仓库的根目录有名为 `.gitlab-ci.yml` 的文件
- 项目配置了 `Runner`

`.gitlab-ci.yml` 告诉了 `Runner` 做什么，runner 就是 run job

`pipeline` (流水线) 是 CI 的顶层单位，包含了 `jobs (任务)` 和 `stages (阶段)`

- jobs (what to do)
- stages (when to run the jobs)
  - 定义了 jobs 执行的顺序，关系(如串行、并行)

### `.gitlab-ci.yml`

- tags 用来指定使用特定的 runner 来执行 jobs
- only/except 来指定 jobs 的触发条件
- cache 用来在 jobs 之间做一些文件/目录的缓存
- artifacts jobs 完成之后的产物，可以在 gitlab 中访问到，可以设置过期时间

#### [tags](https://docs.gitlab.com/ee/ci/yaml/README.html#tags)

```yml
job:
  tags:
    - ruby
    - postgres
```

#### [cache](https://docs.gitlab.com/ee/ci/yaml/README.html#cache)

cache 用来在 jobs 之间做一些文件/目录的缓存

policy 默认是 pull-push，会在 job 开始之前拉缓存，job 结束之后推缓存，对于不需要更新缓存的操作，设置为 pull 即可

### Gitlab 相关工具/功能

- 手动触发 pipeline
- 查看 jobs 的结果和日志
- 验证 `.gitlab-ci.yml` 文件的合法性

## 参考

- [基于 GitLab CI 搭建前端自动构建环境(这里面有核心概念的图示，非常好理解)](https://juejin.im/post/5b470318e51d451993588415)
- [Gitlab CI/CD](https://docs.gitlab.com/ee/ci/README.html)
- [.gitlab-ci.yml文件的配置](https://docs.gitlab.com/ee/ci/yaml/README.html)
