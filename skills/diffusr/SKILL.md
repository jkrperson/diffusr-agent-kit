---
name: diffusr
description: Make videos, images, songs, Recasts and ads with diffusr (MCP or REST API). Use for any task that generates media through diffusr - make_video, make_image, edit_image, recast, make_music, creator_video, run_template, upload_asset, get_result - or asks to connect to, price, or test the diffusr API.
---

# diffusr

diffusr makes ad creative: video, images, image edits, Recasts, songs.
Every make call costs **credits (1 credit = $0.01)**. Real money unless you
use a test key.

## Connect

- **MCP (preferred):** server `https://app.diffusr.ai/api/mcp`, sign in with
  OAuth when the client asks. No key to copy.
- **REST:** `https://app.diffusr.ai/api/v2/<tool>`, header
  `Authorization: Bearer $DIFFUSR_KEY`. Keys: app.diffusr.ai, Developers.
- **Test keys (`dfsr_test_...`) are free:** same calls, same checks and
  prices, fake results, nothing is spent. Use one while building or
  evaluating. Live keys (`dfsr_live_...`) spend real credits.

## Rules (always)

1. **Dry-run first.** Every make tool takes `dry_run: true`: it checks the
   whole request, returns the price, makes nothing, spends nothing.
2. **Quote the price, then ask the human.** Say the credits (and total for a
   multi-step job) and wait for a yes before any call without `dry_run`.
   A yes covers that quote only. With a test key you may proceed.
3. **Never retry a make call "to be safe".** Each call is a new run and bills
   again. Check `list_results` first. For safe retries send an
   `Idempotency-Key` header (REST) or `idempotency_key` (MCP): the same key
   returns the same run.
4. Leave `model` out unless there is a reason; every tool has a default.
   `list_models` shows the choices.

## Tools

| Tool | Does |
|---|---|
| `make_video` | clip from a prompt; `image_urls` = reference pictures, `audio_urls` = voice clips (Seedance) |
| `make_image` | image from a prompt |
| `edit_image` | change a picture, keep the rest (picture first in `image_urls`) |
| `recast` | same clip, new cast (`video_url`, `image_urls`, `mode` world/swap or `prompt`) |
| `make_music` | a song (about a minute; about 9 credits, `dry_run` gives the exact price) |
| `run_template` | run a whole diffusr template in one call, billed at the outcome rate |
| `list_templates` | templates `run_template` can run, their inputs and a price hint |
| `upload_asset` | a URL diffusr can read, for a file you have |
| `get_result` / `list_results` | status and the finished file's URL |
| `list_models` | models and limits (public) |

## Template or recipe

- A **product ad**, **talking head** or **two-host podcast** up to 30 s, or a
  published **Remix / Recast** video template: use `run_template`. One
  call, one price, diffusr does the stills-then-video run. Read `list_templates`
  for the inputs, dry-run, quote, ask, run.
- Anything else in a template's style (animation looks, longer talking heads,
  custom shots): follow the step-by-step recipe over the direct tools. Skills
  `ugc-talking-head`, `two-host-podcast`, `product-ad`, `animation-looks`,
  `recast`; over MCP the same recipes are prompts and `diffusr://templates/<id>`.

## Flow

1. dry run -> price -> human yes.
2. Real call returns `{ id, status: "PENDING" }`.
3. **Poll** `get_result(id)` every 15-30 s (images 10-30 s, video 1-10 min):
   `SUCCEEDED` -> `output.url`; `FAILED` -> `error.message`. Or pass
   `callback_url` and diffusr POSTs a signed result (Standard Webhooks; verify
   with the SDK's `Diffusr.verifyCallback`) so you need not poll.
4. Give the human the URL. Clips come back one by one; there is no stitch
   tool (join with ffmpeg, re-encoded).

## Files and references

Tools take public URLs. On disk: `upload_asset { content_type, size_bytes }`
(exact bytes), PUT to the signed URL it returns, use `file_url`. An online
image or short clip: `upload_asset { source_url }`. URLs on `media.diffusr.ai`
need no upload. Limits: image 25 MB, video 250 MB, audio 50 MB. Never base64
a file into a call. Recast clips: 10 s on the default model, 30 s on
`seedance-2-5`; trim first.

Seedance references: pictures in `image_urls` are Image 1, Image 2...,
voice clips Audio 1... in list order. Bind each at the top of the prompt.
See skill `seedance-references`.

## Errors and limits

- Bad input, not enough credits, wrong model, missing file: all returned
  **before** anything is spent, each field named. Fix and call again. On low
  credits pass the buy link to the human.
- `429` with `Retry-After`: wait that many seconds. Limits apply per key;
  paid starts are the tightest, reads the loosest. Keys can also carry a
  credit budget; hitting it is an error, not a charge.
- `make_video` returns `warnings` when quoted lines need more seconds than
  `duration_sec` (words / 3 + 1 s): fix before spending.
- Errors carry a stable `code` next to the message and a request id; quote it
  in any bug report.

## Docs

Full reference: https://diffusr.ai/docs (agent index
https://diffusr.ai/docs/llms.txt, every page also as `.md`, OpenAPI at
https://app.diffusr.ai/api/v2/openapi.json). Over MCP: resource
`diffusr://guides/docs`.
