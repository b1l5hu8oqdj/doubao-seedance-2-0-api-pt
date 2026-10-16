# Seedance 2.0 API (`seedance-2.0`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.066 per second** at 480P (720P $0.216 · 1080P $0.3849) — um único endpoint compatível com OpenAI em `https://api.apimart.ai/v1`, sem plano mensal. *(observado em 2026-10-12)*

**[Obter uma chave de API](https://go.apimart.ai/k-a85192)** · **[Preços ao vivo](https://go.apimart.ai/k-5a61b2)** · **[Página do modelo](https://go.apimart.ai/k-aab4e9)** · [⚡ Início rápido em 60 segundos](#quickstart)

**Por que os times usam Seedance 2.0 (`seedance-2.0`) via APIMart**

- **Uma chave, o catálogo inteiro.** A mesma base URL `https://api.apimart.ai/v1` e o mesmo cabeçalho `Authorization` alcançam Seedance 2.0 (`seedance-2.0`) e mais de 300 modelos de imagem, vídeo e linguagem — troque o campo `model`, não o seu cliente.
- **Mínimo de $1, pague conforme o uso.** Sem assinatura e sem plano pré-pago para dimensionar antes: recarregue a partir de $1 e gaste em chamadas.
- **A cobrança volta na resposta.** Toda tarefa concluída informa o valor cobrado (`cost` / `credits_cost`); uma tarefa marcada como `failed` é reembolsada.
- **Assíncrono por design.** Envie, pegue o `task_id`, consulte `GET /v1/tasks/{id}` — batching e retries são trabalho comum de fila, não uma integração sob medida.

<!-- /conv-kit:v1 -->

**Também conhecido como:** Dreamina

Seedance 2.0 is ByteDance's text-to-video and image-to-video model with cinematic motion and HD output.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `seedance-2.0` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-aab4e9) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.04 | 0.04 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-5a61b2) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

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

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-aab4e9) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"seedance-2.0","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "seedance-2.0", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
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

**Qual é o id do modelo Seedance 2.0?**

`seedance-2.0`. Veja a tabela de rotas acima.

**Como é a cobrança?**

**from $0.066 per second** at 480P (720P $0.216 · 1080P $0.3849). A tarefa concluída informa o valor exato no campo `cost`; uma tarefa com falha é reembolsada.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-a85192) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [doubao-seedance-2-0-prompts-pt](https://github.com/b1l5hu8oqdj/doubao-seedance-2-0-prompts-pt)
- **Same model, other languages** — 9 more language builds: 中文 [doubao-seedance-2-0-api-cn](https://github.com/t7phf53f5hs/doubao-seedance-2-0-api-cn) · Deutsch [doubao-seedance-2-0-api-de](https://github.com/rnd5d7u4/doubao-seedance-2-0-api-de) · Español [doubao-seedance-2-0-api-es](https://github.com/jc3a6phjspf-3/doubao-seedance-2-0-api-es) · Français [doubao-seedance-2-0-api-fr](https://github.com/e3udvo/doubao-seedance-2-0-api-fr) · Relay/gateway [doubao-seedance-2-0-api-gateway](https://github.com/emw2cy1k/doubao-seedance-2-0-api-gateway) · Bahasa [doubao-seedance-2-0-api-id](https://github.com/scsa67y7x97nk/doubao-seedance-2-0-api-id) · 日本語 [doubao-seedance-2-0-api-ja](https://github.com/p5s5nph3/doubao-seedance-2-0-api-ja) · 한국어 [doubao-seedance-2-0-api-ko](https://github.com/uu8bu/doubao-seedance-2-0-api-ko) · Русский [doubao-seedance-2-0-api-ru](https://github.com/z6gquntrrfq/doubao-seedance-2-0-api-ru)
- **More models in this language** — [midjourney-api-pt](https://github.com/l-ca0/midjourney-api-pt) · [gemini-2-5-flash-image-preview-api-pt](https://github.com/ycx8l0/gemini-2-5-flash-image-preview-api-pt) · [nano-banana-api-pt](https://github.com/erujt71i1lx/nano-banana-api-pt) · [gemini-3-1-flash-image-preview-api-pt](https://github.com/hutjwux0nt9h/gemini-3-1-flash-image-preview-api-pt) · [nano-banana-2-api-pt](https://github.com/ydkd8kdkl8n/nano-banana-2-api-pt) · [gemini-3-pro-image-preview-api-pt](https://github.com/qss2d-66qb2n7/gemini-3-pro-image-preview-api-pt)
- **Topic hubs** — every build of this model, all languages: [doubao-seedance-2-0](https://github.com/topics/doubao-seedance-2-0) · [doubao-seedance-2-0-api](https://github.com/topics/doubao-seedance-2-0-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-aab4e9> | `apimart.ai/pt/model/doubao-seedance-2-0` |
| Live pricing | <https://go.apimart.ai/k-5a61b2> | `apimart.ai/pt/pricing` |
| Get an API key | <https://go.apimart.ai/k-a85192> | `apimart.ai/pt/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Seedance 2.0 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
