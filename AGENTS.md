# diffusr for agents

diffusr makes ad creative: video, images, image edits, Recasts, songs.

## Connect

- MCP: `https://app.diffusr.ai/api/mcp` (sign in with OAuth), or
- REST: `https://app.diffusr.ai/api/v2/<tool>` with `Authorization: Bearer $DIFFUSR_KEY`.
- Test keys (`dfsr_test_...`) give free fake results: use one while building.
  Live keys (`dfsr_live_...`) spend real credits.

## Rules

1. Every make tool (`make_video`, `make_image`, `edit_image`, `recast`,
   `make_music`, `creator_video`, `run_template`) takes `dry_run: true`: it
   checks the request, returns the price, and spends nothing. Always call it first.
2. Quote the price in credits (1 credit = $0.01) and ask the human before any
   call without `dry_run`. A yes covers that quote only.
3. Never retry a make call "to be safe": each call is a new run. Send an
   `Idempotency-Key` header (REST) or `idempotency_key` (MCP) for safe retries.
4. Results are async: poll `get_result(id)` every 15-30 s, or pass `callback_url`.
5. Local files go through `upload_asset` first. Never base64 a file into a call.
6. `429` has `Retry-After`: wait that long.
7. A product ad, talking head or two-host podcast up to 30 s, or a published
   Remix/Recast template: use `run_template` (`list_templates` shows the
   inputs). Other styles are step-by-step recipes over the direct tools.

## Docs

- https://diffusr.ai/docs
- Agent index: https://diffusr.ai/docs/llms.txt (every page also as `.md`)
- OpenAPI: https://app.diffusr.ai/api/v2/openapi.json
- Skills with the full recipes: `agent-kit/skills/` (`diffusr`, `ugc-talking-head`,
  `two-host-podcast`, `product-ad`, `recast`, `animation-looks`, `seedance-references`).
