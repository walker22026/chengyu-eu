---
title: "Building a Free, OpenAI-Compatible API on Top of Cloudflare Workers AI"
date: 2024-05-01T06:48:10+00:00
summary: "Wrapping Cloudflare's free Workers AI models (including Qwen) behind an OpenAI-compatible endpoint, so existing front-ends don't need to change a line of code."
tags: ["Cloudflare", "LLM", "AI"]
categories: ["Blog"]
---

Plenty of large language models are usable for free these days, but a reasonably stable API service still usually costs money. Cloudflare — famously generous when it comes to free tiers — offers a decent free allowance here too. Its "Beta" model tier is free, and remarkably that even includes a large model like Alibaba's Qwen.

So here's how to wrap Cloudflare's AI service behind an OpenAI-compatible API, so a front-end can start using it without any code changes at all.

### Why this approach?

As the field moves on, smaller and cheaper language models look increasingly competitive against OpenAI's GPT-3.5/GPT-4 APIs. A lot of developers understandably don't want to rewrite their entire codebase just to try a new model. Cloudflare Workers is also a great place to host both the AI service and the API layer, so I built an OpenAI-compatible API on top of it — letting developers swap in new LLMs without touching their existing code.

### Compatibility and implemented APIs

Implemented, or planned:

- Completions
- Chat Completions
- Audio Transcription
- Embeddings
- Audio Translation
- Image Generation
- File handling, with Assistants support
- Storing assistants, threads, and messages in a D1 database

Transcription runs on Whisper, language identification on Llama 2, and translation on m2m-100.

### Deployment steps

I deployed this from a MacBook; the steps are the same on Ubuntu. Note that this deployment flow needs a browser at one point, so SSH-ing into a remote box won't work without some adjustment.

**1. Clone the repo**

```
git clone https://github.com/chand1012/openai-cf-workers-ai
cd openai-cf-workers-ai
```

**2. Edit the config**

Open `wrangler.toml` and set `CLOUDFLARE_ACCOUNT_ID` to your own Cloudflare account ID:

```
CLOUDFLARE_ACCOUNT_ID = "your-account-id-here" # replace with your own
```

You can find this ID on the Cloudflare dashboard.

![Finding the Cloudflare account ID](https://images.chengyu.eu/file/61ba56c51890282cac3a9.png)

**3. Install dependencies and deploy**

1) `yarn` — I didn't have yarn installed, so this failed the first time. Install it with:

```
brew install yarn
```

If that complains about a missing Node.js:

```
brew install node
```

Once installed, the earlier command runs fine.

![Installing dependencies](https://images.chengyu.eu/file/fba46ec183c1c1eec03e9.png)

2) `yarn init-prod` — run this once. It'll prompt for authorization in a browser window; just click Allow. If authorization fails, you'll need to grant access in Cloudflare first:

- Open the Cloudflare dashboard.
- Go to the **R2 Storage** section (in the left-hand menu, or search for "R2").
- Enable R2 if it isn't already, following the prompts.
- Re-run `yarn init-prod`.

![Authorization error](https://images.chengyu.eu/file/fff0df3bc46b8c1fa3b18.png)

3) Once authorized, run `yarn deploy` and wait for the app to finish deploying.

**4. Set the access tokens**

- **Option 1**: use `wrangler` to set `ACCESS_TOKEN` and `CLOUDFLARE_API_TOKEN` directly. You can create a new API token from the Cloudflare dashboard.
- **Option 2 (recommended)**: open the Workers & Pages section in the Cloudflare dashboard, select the project you just created, go to Settings, and add `ACCESS_TOKEN` and `CLOUDFLARE_API_TOKEN` there. `ACCESS_TOKEN` is the credential your own clients will use to call this API; `CLOUDFLARE_API_TOKEN` is Cloudflare's own Workers AI token — you can create one at <https://dash.cloudflare.com/profile/api-tokens>.

![Setting environment variables](https://images.chengyu.eu/file/fb121fae8750faea83f93.png)

On that page, create a new token and pick the "Workers AI" template.

![Creating a token from the template](https://images.chengyu.eu/file/cb1d3f952955b5b49898e.png)

Once created, the token is shown once — copy it down, paste it into the variable, and redeploy. Done.

### Usage

See the OpenAI API docs for the general shape of requests. A couple of examples:

```shell
curl https://openai-cf.yourusername.workers.dev/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <any string value you set>" \
  -d '{
    "model": "@cf/meta/llama-2-7b-chat-int8",
    "messages": [
      { "role": "system", "content": "You are a helpful assistant." },
      { "role": "user", "content": "Hello!" }
    ]
  }'
```

```shell
curl https://openai-cf.<your-project-domain>.workers.dev/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <your access token>" \
  -d '{
    "model": "@cf/qwen/qwen1.5-7b-chat-awq",
    "messages": [
      { "role": "system", "content": "You are a helpful assistant." },
      { "role": "user", "content": "Who are you?" }
    ]
  }'
```

### Caveats

- Token usage isn't tracked yet — it always reports zero.
- Stop sequences aren't supported yet.
