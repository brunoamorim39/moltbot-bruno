# Fork Customizations (moltbot-bruno)

This document tracks customizations made to this fork that **must be preserved** when merging from upstream.

## Critical Files to Review After Upstream Merge

### `wrangler.jsonc`

**R2 Bucket Configuration:**
```jsonc
"r2_buckets": [
  {
    "binding": "MOLTBOT_BUCKET",
    "bucket_name": "moltbot-bruno",        // NOT "moltbot-data"
    "preview_bucket_name": "moltbot-bruno"
  }
]
```

**Worker Name:**
```jsonc
"name": "moltbot-bruno"  // NOT "moltbot" or other upstream default
```

### `src/config.ts`

**Default bucket name fallback:**
```typescript
export function getR2BucketName(env?: { R2_BUCKET_NAME?: string }): string {
  return env?.R2_BUCKET_NAME || 'moltbot-bruno';  // NOT 'moltbot-data'
}
```

## Required Wrangler Secrets

These secrets must be configured via `wrangler secret put <NAME>`:

| Secret | Purpose |
|--------|---------|
| `NVIDIA_API_KEY` | NVIDIA API key for Kimi K2.5 model |
| `ANTHROPIC_API_KEY` | Fallback Anthropic API key |
| `CF_ACCESS_AUD` | Cloudflare Access audience tag |
| `CF_ACCESS_TEAM_DOMAIN` | Cloudflare Access team domain |
| `CF_ACCOUNT_ID` | Cloudflare account ID for R2 |
| `MOLTBOT_GATEWAY_TOKEN` | Gateway authentication token |
| `R2_ACCESS_KEY_ID` | R2 bucket access key |
| `R2_SECRET_ACCESS_KEY` | R2 bucket secret key |

## Model Configuration

This fork uses **NVIDIA Kimi K2.5** as the default model (not Claude Haiku).

For this to work:
1. `NVIDIA_API_KEY` must be set as a secret
2. `AI_GATEWAY_BASE_URL` should NOT be set (it would override NVIDIA)

The model selection logic is in `start-moltbot.sh` lines 230-299.

## Merge Checklist

After running `git fetch upstream && git merge upstream/main`:

- [ ] Check `wrangler.jsonc` - bucket_name should be `moltbot-bruno`
- [ ] Check `wrangler.jsonc` - worker name should be `moltbot-bruno`
- [ ] Check `src/config.ts` - default bucket should be `moltbot-bruno`
- [ ] Verify secrets are still configured: `npx wrangler secret list`
- [ ] Test deployment before pushing to main
