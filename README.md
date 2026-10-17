# Flow Music API (`flowmusic`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/audio-waveform.svg" width="820" alt="Music generation request path through the APIMart audio endpoint">
</p>

> **from $0.01 per generation** on the APIMart route — one OpenAI-compatible endpoint at `https://api.apimart.ai/v1`, no monthly plan required. *(observed 2026-10-12)*

**[Get an API key](https://go.apimart.ai/k-2ddd3d)** · **[Live pricing](https://go.apimart.ai/k-c8965f)** · **[Model page](https://go.apimart.ai/k-7c74ff)** · [⚡ 60-second quickstart](#quickstart)

**Why teams call Flow Music (`flowmusic`) through APIMart**

- **One key, entire catalog.** The same `https://api.apimart.ai/v1` base URL and `Authorization` header reach Flow Music (`flowmusic`) and 300+ other image, video and language models — switch the `model` field, not your client.
- **$1 minimum, pay as you go.** No subscription and no prepaid plan to size up front: top up from $1 and spend it on calls. There is no free quota to burn through first, so the price in this table is the price you pay.
- **The charge comes back in the response.** Every call reports the amount billed (`cost` / `credits_cost`), so a spend number is read per call instead of guessed at month end.
- **Async by design.** Submit, take the `task_id`, poll `GET /v1/tasks/{id}` — batching and retries are ordinary queue work, not a bespoke integration.

<!-- /conv-kit:v1 -->



Flow Music API on APIMart provides a production-ready AI music generation workflow for creating full songs from lyrics and style prompts, with vocals or instrumental output and asynchronous task polling.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Music generation (async task) | `flowmusic` | per generated track, by operation | see the [model page](https://go.apimart.ai/k-7c74ff) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/music/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.075 | 0.06 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-c8965f) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| model | string | — | the music model id |
| title | string | — | track title |
| sound_prompt | string | — | style / instrumentation description (one of sound_prompt or lyrics is required) |
| lyrics | string | — | lyrics text with section tags; leave empty for instrumental |
| bpm | string | number | — | tempo of the generated track |
| length | integer | — | target duration in seconds |
| audio_url | string | — | source audio for extend / cover / stem operations |
| callback_url | string | — | webhook called when the task completes |

The table lists the fields accepted by the APIMart `music/generations` endpoint. Check the [model page](https://go.apimart.ai/k-7c74ff) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/music/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"flowmusic","title":"My Song","sound_prompt":"upbeat pop music with piano","bpm":"120","length":60}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/music/generations", headers=HEADERS, timeout=60, json={
    "model": "flowmusic", "title": "My Song", "sound_prompt": "upbeat pop music with piano", "bpm": "120", "length": 60,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["audio"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**What is the Flow Music model id?**

`flowmusic`. See the route table above.

**How is it billed?**

**from $0.01 per generation** on the APIMart route. The completed task reports the exact amount in its `cost` field; a failed task is refunded.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-2ddd3d) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [flow-music-prompts](https://github.com/pxxezcu4cnmhmi/flow-music-prompts)
- **Same model, other languages** — 10 more language builds: 中文 [flow-music-api-cn](https://github.com/yjdu5jxvsupfl/flow-music-api-cn) · Deutsch [flow-music-api-de](https://github.com/ow2s8cc1h/flow-music-api-de) · Español [flow-music-api-es](https://github.com/bg10uuih/flow-music-api-es) · Français `flow-music-api-fr` · Relay/gateway `flow-music-api-gateway` · Bahasa [flow-music-api-id](https://github.com/jyp5o6wi5bbg1/flow-music-api-id) · 日本語 `flow-music-api-ja` · 한국어 [flow-music-api-ko](https://github.com/bqiqlq4/flow-music-api-ko) · Português `flow-music-api-pt` · Русский [flow-music-api-ru](https://github.com/tnscfa/flow-music-api-ru)
- **More models in this language** — [grok-imagine-1-5-api](https://github.com/igu3s5e5x90wx/grok-imagine-1-5-api) · [grok-imagine-2-0-api](https://github.com/tv8s57ojjaw/grok-imagine-2-0-api) · [grok-image-api](https://github.com/hpcsd0apt/grok-image-api) · [grok-imagine-image-quality-api](https://github.com/xt5chw51l/grok-imagine-image-quality-api) · [imagen-4-api](https://github.com/lrk0lbrti/imagen-4-api) · [qwen-image-2-0-pro-api](https://github.com/usen4/qwen-image-2-0-pro-api)
- **Topic hubs** — every build of this model, all languages: [flow-music](https://github.com/topics/flow-music) · [flow-music-api](https://github.com/topics/flow-music-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-7c74ff> | `apimart.ai/model/flow-music` |
| Live pricing | <https://go.apimart.ai/k-c8965f> | `apimart.ai/pricing` |
| Get an API key | <https://go.apimart.ai/k-2ddd3d> | `apimart.ai/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Flow Music is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
