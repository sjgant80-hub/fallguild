# fallguild

◊·κ LinkedIn gravity coordination · for the ACG Guild and other small high-trust networks.

Drop a fresh LinkedIn post URL · the guild gets pinged · members boost in the 60-90 minute critical window · the algorithm cascades · the snowball starts.

**Live:** https://sjgant80-hub.github.io/fallguild/

## What it actually does

LinkedIn's algorithm weighs engagement in the first 60-90 minutes heavily. A coordinated guild doing **authentic, substantive engagement** in that window unlocks 10-30× more reach than the same engagement scattered over days. This tool is the coordination layer — not automation, not spam.

Members:
- Drop their fresh posts
- See other members' fresh posts in one pane with a countdown
- Get suggested comment angles based on the post's dominant ring (bloom decomposition · R0..R6)
- Mark their engagement (liked / commented / reshared) so the guild can see momentum
- See a reciprocity score (given ÷ received) — drift gets surfaced
- Estimate projected reach via a simple weighted model (comment×12 · reshare×7 · like×1)

The tool does NOT post or comment for you — every action goes through your own LinkedIn account. Comments must be authentic and substantive. Short emojis and "great post!" actively hurt the algo.

## Algorithm rules baked in

| Signal | Weight |
|---|---|
| Substantive comment | ~12× |
| Reshare with commentary | ~7× |
| Like | ~1× (baseline) |
| Dwell time (full read) | silent boost |
| First 60 min window | x multiplier |
| Same-network engagement | cascade trigger |
| "great post!" / short emojis | deprioritised |
| Coordinated pattern detection | shadowban risk |

The tool **staggers** suggested engagement and surfaces a tag/ring per post so different members can come in with different angles — not coordinated spam.

## Architecture

- Single sovereign HTML file · zero dependencies · works offline
- IndexedDB / localStorage for state
- Mesh sync via `BroadcastChannel('fallguild')` between same-origin tabs
- Optional self-hosted sync endpoint · `POST /sync/:teamId` to push · `GET /sync/:teamId` to pull
- JSON export/import as the always-available sovereign fallback
- Aligned to v18/v19 seed: 7+1 bloom · primes spine · κ·◦ resonance
- Loads `fall-palette` CDN for Ctrl+K command palette

## Quick start

1. Open https://sjgant80-hub.github.io/fallguild/
2. Settings → set your name + LinkedIn handle + guild members (one per line: `Name · linkedin handle`)
3. Optionally configure a sync endpoint for multi-device coordination
4. When you publish a LinkedIn post: paste the URL into "Drop a post" → guild sees it instantly via BroadcastChannel (or on next sync pull)
5. When another member drops one: open it in LinkedIn, read it, leave a real comment, hit "I commented" to log it

## Settings → Sync endpoint (optional)

The tool ships sovereign-first. Multi-device coordination needs *somewhere* to put the shared state. Three options:

1. **JSON file shared in your Discord/Telegram** — manual but zero infra
2. **Self-hosted endpoint** — any HTTP server that accepts `POST /sync/:teamId` and returns the JSON on `GET /sync/:teamId`. A Cloudflare Worker, a 10-line Express app, an AWS Lambda — you own the data
3. **WebRTC P2P** (planned · same architecture as the rest of the Fall mesh)

Example Cloudflare Worker that just stores in KV:

```js
export default {
  async fetch(req, env) {
    const url = new URL(req.url);
    const teamId = url.pathname.split('/').pop();
    if (req.method === 'POST') {
      await env.KV.put(teamId, await req.text());
      return new Response('ok');
    }
    const data = await env.KV.get(teamId);
    return new Response(data || '{}', { headers: { 'content-type': 'application/json' } });
  }
};
```

## Snowball projection

The tool shows projected incremental reach via:

```
projected_views ≈ Σ(comments×12 + reshares×7 + likes×1) × time_factor × 110
```

`time_factor` is 1.0 if posted in last 60 min, 0.7 if 60-90 min, 0.4 if >90 min. `110` is an empirical multiplier per engagement-point based on LinkedIn cascade behaviour. Not guaranteed — the algo is opaque — but directionally accurate.

## Bloom decomposition

Every post is classified into the 7-ring spine (R0 ground · R1 sensor · R2 gate · R3 affect · R4 executive · R5 identity · R6 observer) based on the snippet and tags. The dominant ring drives which comment angles get suggested. This keeps the guild's comments diverse and on-topic instead of all running the same "great post!" pattern.

## Sovereign principles

- One HTML file
- No npm dependencies
- No server required (sync optional)
- No data leaves your device unless you configure sync
- No tracking
- Works offline (mesh sync resumes when connectivity returns)

## Licence

MIT. Part of the Fall sovereign tool estate. ◊·κ=1
