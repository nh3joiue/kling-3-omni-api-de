# Kling 3.0 Omni API (`kling-v3-omni`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.0672 per second** on the APIMart route — ein einziger OpenAI-kompatibler Endpoint unter `https://api.apimart.ai/v1`, kein Monatsabo nötig. *(erfasst am 2026-10-12)*

**[API-Key erhalten](https://go.apimart.ai/k-f7a116)** · **[Live-Preise](https://go.apimart.ai/k-8fc6e7)** · **[Modellseite](https://go.apimart.ai/k-b83bc1)** · [⚡ Quickstart in 60 Sekunden](#quickstart)

**Warum Teams Kling 3.0 Omni (`kling-v3-omni`) über APIMart nutzen**

- **Ein Key, der gesamte Katalog.** Dieselbe Base-URL `https://api.apimart.ai/v1` und derselbe `Authorization`-Header erreichen Kling 3.0 Omni (`kling-v3-omni`) und über 300 weitere Bild-, Video- und Sprachmodelle — Sie wechseln das `model`-Feld, nicht Ihren Client.
- **$1 Minimum, Pay-as-you-go.** Kein Abo und kein Prepaid-Tarif, den Sie vorab dimensionieren müssen: ab $1 aufladen und in Calls verbrauchen.
- **Die Abrechnung kommt in der Antwort zurück.** Jede abgeschlossene Aufgabe meldet den berechneten Betrag (`cost` / `credits_cost`); eine als `failed` markierte Aufgabe wird erstattet.
- **Asynchron by Design.** Absenden, `task_id` entgegennehmen, `GET /v1/tasks/{id}` pollen — Batching und Retries sind gewöhnliche Queue-Arbeit, keine Sonderintegration.

<!-- /conv-kit:v1 -->



Kling V3 Omni API on APIMart offers Kuaishou's AI video generation model — kling-v3-omni's all-in-one capabilities with text, image and video inputs, and up to 15-second video generation. One APIMart key gives you access to its unified creative workflow.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `kling-v3-omni` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-b83bc1) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.084 | 0.0672 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-8fc6e7) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | the shot description; camera and subject motion belong here |
| `duration` | integer | route default | clip length in seconds — billing is per generated second |
| `resolution` | string | route default | 480P / 720P / 1080P or the route’s own tiers |
| `size` / `aspect_ratio` | string | `16:9` | output framing |
| `image_urls` | string[] | — | first frame, first + last frame, or style references, where supported |
| `seed` | integer | — | fix the seed to reproduce a take |

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-b83bc1) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"kling-v3-omni","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "kling-v3-omni", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["videos"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Wie lautet die Modell-id von Kling 3.0 Omni?**

`kling-v3-omni`. Siehe Routentabelle oben.

**Wie wird abgerechnet?**

**from $0.0672 per second** on the APIMart route. Die abgeschlossene Aufgabe meldet den genauen Betrag im Feld `cost`; eine fehlgeschlagene Aufgabe wird erstattet.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-f7a116) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [kling-3-omni-prompts-de](https://github.com/nh3joiue/kling-3-omni-prompts-de)
- **Same model, other languages** — 10 more language builds: English [kling-3-omni-api](https://github.com/cub-9p/kling-3-omni-api) · 中文 [kling-3-omni-api-cn](https://github.com/pq4mk9/kling-3-omni-api-cn) · Español [kling-3-omni-api-es](https://github.com/nurca-m/kling-3-omni-api-es) · Français [kling-3-omni-api-fr](https://github.com/t0e084d/kling-3-omni-api-fr) · Relay/gateway `kling-3-omni-api-gateway` · Bahasa [kling-3-omni-api-id](https://github.com/f7a18/kling-3-omni-api-id) · 日本語 [kling-3-omni-api-ja](https://github.com/j-vx0q/kling-3-omni-api-ja) · 한국어 [kling-3-omni-api-ko](https://github.com/qlokvy3b5tw3z/kling-3-omni-api-ko) · Português [kling-3-omni-api-pt](https://github.com/l955saiv/kling-3-omni-api-pt) · Русский `kling-3-omni-api-ru`
- **More models in this language** — [midjourney-api-de](https://github.com/h5fd0cofy/midjourney-api-de) · [gemini-2-5-flash-image-preview-api-de](https://github.com/kn6f69s2b8j9/gemini-2-5-flash-image-preview-api-de) · [nano-banana-api-de](https://github.com/uh6cob8op/nano-banana-api-de) · [gemini-3-1-flash-image-preview-api-de](https://github.com/k8wq8pd46e5ku/gemini-3-1-flash-image-preview-api-de) · [nano-banana-2-api-de](https://github.com/mocpl3/nano-banana-2-api-de) · [gemini-3-pro-image-preview-api-de](https://github.com/cmpxx2w/gemini-3-pro-image-preview-api-de)
- **Topic hubs** — every build of this model, all languages: [kling-3-omni](https://github.com/topics/kling-3-omni) · [kling-3-omni-api](https://github.com/topics/kling-3-omni-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-b83bc1> | `apimart.ai/de/model/kling-v3-omni` |
| Live pricing | <https://go.apimart.ai/k-8fc6e7> | `apimart.ai/de/pricing` |
| Get an API key | <https://go.apimart.ai/k-f7a116> | `apimart.ai/de/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Kling 3.0 Omni is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
