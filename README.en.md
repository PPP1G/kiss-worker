# KISS-WORKER

A simple data sync service for [KISS-Translator](https://github.com/fishjar/kiss-translator).

There are two deployment methods to choose from:

## `cloudflare workers` deployment method

### Prerequisites

- [Cloudflare](https://www.cloudflare.com/) account
- Install `git` + `nodejs` locally when deploying
- A domain name (optional)

### Deployment steps

1. Log in to the Cloudflare management panel and go to the path `dashboard > select Workers & Pages > KV`. Create a namespace with whatever name you want. After creation, a `namespace ID` will be obtained.

2. Clone the project, modify the `wrangler.toml` file, and replace the `namespace ID` obtained in the previous step to the position of `id`.

```toml
# wrangler.toml
kv_namespaces = [
    { binding = "KV", id = "replace you id here!!!" }
]
```

3. Execute the following commands in sequence. You may need to authorize Cloudflare when deploying for the first time. The `secret` command prompts for your password.

```sh
npm install
npm run deploy
npm run secret
```

4. (Optional) Log in to the Cloudflare management panel, enter the path `dashboard > select Workers & Pages > kiss-worker`, click the `Trigger` tab, and then click `Add Custom Domain` to add a domain name to access.

Existing installations can upgrade the same Worker in place. Keep the Worker name and KV binding in `wrangler.toml`, then run `npm run deploy`. Cloudflare creates the Durable Object automatically and existing KV records migrate lazily when accessed. New data is authoritative in Durable Object storage, so export it before rolling back to an older Worker version.

## `docker` deployment method

### Prerequisites

- Own server
- `docker` related knowledge

### Deployment steps

1. Clone the project and create a `.env` file in the project directory with your own password.

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

2. Execute the following command to start

```sh
docker-compose up -d
```
