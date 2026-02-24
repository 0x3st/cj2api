# ChatJimmy2API 一键部署
[![License: AEPL v1.0](https://img.shields.io/badge/License-AEPL_v1.0-red.svg)](LICENSE)

把 `https://chatjimmy.ai/api/chat` 包装成 OpenAI 兼容接口，可部署到：

- Cloudflare Worker
- Vercel Edge Function
- Netlify Function

用于沉浸式翻译等只支持 OpenAI 协议的客户端。

## 支持接口

- `GET /health`
- `GET /v1/models`
- `POST /v1/chat/completions`
  - `stream: false` 返回标准 JSON
  - `stream: true` 返回 SSE（单块内容 + `[DONE]`）

## 一键部署按钮

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https%3A%2F%2Fgithub.com%2F0x3st%2Fcj2api)


[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2F0x3st%2Fcj2api&env=OPENAI_API_KEY&envDescription=Enter%20the%20API%20key%20clients%20must%20send%20as%20Bearer%20token.)


[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https%3A%2F%2Fgithub.com%2F0x3st%2Fcj2api#OPENAI_API_KEY=)

以上三家都按“只要求 `OPENAI_API_KEY`”配置。

一键部署后请确认这项环境变量：

- `OPENAI_API_KEY`（建议必填，避免接口裸奔）

## 统一环境变量

- `OPENAI_API_KEY`: 开启鉴权（客户端需 `Authorization: Bearer xxx`）
- `CHATJIMMY_URL`: 默认 `https://chatjimmy.ai/api/chat`
- `CHATJIMMY_MODEL`: 默认 `llama3.1-8B`
- `CHATJIMMY_TOP_K`: 默认 `8`
- `CHATJIMMY_MODELS`: 逗号分隔模型列表（用于 `/v1/models`）
- `ALLOWED_ORIGIN`: CORS 允许源，默认 `*`

## 1) Cloudflare Worker

```bash
npm install
npx wrangler login
npx wrangler secret put OPENAI_API_KEY
npx wrangler deploy
```

入口文件：`src/worker.js`

## 2) Vercel

已提供：

- Edge 入口：`api/v1-chat-completions.js`
- Edge 入口：`api/v1-models.js`
- Edge 入口：`api/health.js`
- 根路径入口：`api/index.js`
- 路由重写：`vercel.json`

部署：

```bash
vercel
```

在 Vercel 项目设置里添加上述环境变量即可。

## 3) Netlify

已提供：

- Function 入口：`netlify/functions/openai.js`
- 路由重写：`netlify.toml`

部署：

```bash
netlify deploy --prod
```

在 Netlify 环境变量里添加上述变量即可。

## OpenAI 兼容调用示例

把 `YOUR_BASE_URL` 换成你的域名（不要带结尾 `/`）。

```bash
curl https://YOUR_BASE_URL/v1/chat/completions \
  -H "Authorization: Bearer YOUR_OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.1-8B",
    "messages": [
      {"role":"system","content":"You are a cat, start with meow"},
      {"role":"user","content":"给我推荐晚饭"}
    ],
    "stream": false
  }'
```

## 沉浸式翻译配置

- Base URL: `https://YOUR_BASE_URL/v1/chat/completions`
- API Key: `YOUR_OPENAI_API_KEY`
- Model: `llama3.1-8B`（或你自定义模型）

## License

AEPL v1.0
