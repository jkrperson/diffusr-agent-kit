# diffusr agent kit

Skills, a Claude Code plugin, and rules that teach an AI agent to make videos,
images, songs and ads with [diffusr](https://diffusr.ai) the right way: price
first, ask before spending, pick the right tool. Docs for agents:
https://diffusr.ai/docs (index: https://diffusr.ai/docs/llms.txt).

Start with a **test key** (`dfsr_test_...`): same calls, same prices, fake
results, nothing spent. Get keys in the app under Developers.

## Install

**Claude Code (plugin).** Adds the skills and the diffusr MCP server (OAuth):

```
claude plugin marketplace add jkrperson/diffusr-agent-kit
claude plugin install diffusr@diffusr
```

**Codex and other AGENTS.md readers.** Copy `AGENTS.md` into your project (or
append it to yours) and copy `skills/` where your agent reads skills. Connect
the MCP server `https://app.diffusr.ai/api/mcp`, or use the REST API with a key.

**Cursor.** Copy `cursor/diffusr.mdc` to `.cursor/rules/`, and add the MCP
server `https://app.diffusr.ai/api/mcp` in Cursor's MCP settings.

**Any agent that reads Agent Skills** (folders with a `SKILL.md`): copy the
folders in `skills/` to its skills directory.

## Skills

| Skill | Use it to |
|---|---|
| `diffusr` | Connect, test vs live keys, dry-run and price first, polling vs callbacks, uploads, errors, `run_template` vs recipes |
| `ugc-talking-head` | One or two people saying a script to a phone camera |
| `two-host-podcast` | Two hosts in a studio, one take up to 30 s |
| `product-ad` | A 20-30 s UGC product ad from a product photo |
| `recast` | Same clip, new cast or place |
| `animation-looks` | A film in an animated look (claymation, LEGO, ...) |
| `seedance-references` | Make and pass reference pictures and voice clips for Seedance |

`diffusr` is hand-written. The other six are generated from the same recipe
code the diffusr MCP server serves, so the two never disagree. This repo is a
copy of `agent-kit/` in diffusr's own repo: change things there, not here.

## Evals

`evals/` holds scenarios for `claude plugin eval agent-kit --ablation none`
(they use mocked tools and assert dry-run first, the right tool, a quoted price,
and no spend without a yes). Run them with a test setup only.
