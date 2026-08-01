# KISS-WORKER

适用于 [KISS-Translator](https://github.com/fishjar/kiss-translator) 的简单的数据同步服务。

有两种部署方式可供选择：

## `cloudflare workers` 部署方式

### 前提条件

- [Cloudflare](https://www.cloudflare.com/) 帐号
- 部署时本地安装 `git` + `nodejs`
- 一个域名（可选）

### 部署步骤

1、登录 Cloudflare 管理面板，进入路径 `dashboard > select Workers & Pages > KV`。创建一个命名空间，名称随意。创建完成后将获得一个`命名空间 ID`。

2、克隆项目，修改 `wrangler.toml` 文件，将前面步骤获取到的`命名空间 ID` 替换到`id`的位置。

```toml
# wrangler.toml
kv_namespaces = [
    { binding = "KV", id = "replace you id here!!!" }
]
```

3、依次执行下面的命令。首次部署时可能需要连接到 Cloudflare 授权，`secret` 命令会要求设定自己的密码。

```sh
npm install
npm run deploy
npm run secret
```

4、（可选）登录 Cloudflare 管理面板，进入路径 `dashboard > select Workers & Pages > kiss-worker`，点击 `触发器`选项卡，再点击`添加自定义域`添加访问的域名。

从旧版本升级时无需重新创建 Worker。保持 `wrangler.toml` 中的 Worker 名称和 KV 绑定不变并执行 `npm run deploy`，Durable Object 会在首次部署时自动创建，已有 KV 数据会在访问时按键迁移。升级后新增数据以 Durable Object 为准，回滚旧代码前请先导出新增数据。

## `docker` 部署方式

### 前提条件

- 自有服务器
- `docker`相关知识

### 部署步骤

1、克隆项目，在项目目录创建 `.env` 文件并设置你自己的密码。

```env
APP_KEY=replace-with-a-random-secret
```

```yml
services:
  kiss-worker:
    image: fishjar/kiss-worker
    environment:
      PORT: 8080
      APP_KEY: "${APP_KEY:?APP_KEY is required}"
      APP_DATAPATH: data
    ports:
      - 8080:8080
    volumes:
      - ./data:/app/data
```

2、执行以下命令启动

```sh
docker-compose up -d
```
