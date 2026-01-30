# Quick Start Guide - MoonshotAI Kimi K2.5 via OpenRouter

This is a condensed version of the setup. For detailed instructions, see [SETUP.md](./SETUP.md).

## Prerequisites Checklist

- [ ] Cloudflare account with Workers Paid Plan ($5/month)
- [ ] OpenRouter account with credits and API key
- [ ] Node.js 18+ installed

## Quick Setup Steps

### 1. Install Dependencies
```bash
npm install
```

### 2. Set Up Secrets

```bash
# OpenRouter via AI Gateway (Required)
npx wrangler secret put AI_GATEWAY_API_KEY
# Enter your OpenRouter API key

npx wrangler secret put AI_GATEWAY_BASE_URL
# Enter: https://gateway.ai.cloudflare.com/v1/{account_id}/{gateway_id}/openrouter
# (Get this from AI Gateway dashboard)

# Gateway Token (Required)
export MOLTBOT_GATEWAY_TOKEN=$(openssl rand -hex 32)
echo "Save this token: $MOLTBOT_GATEWAY_TOKEN"
echo "$MOLTBOT_GATEWAY_TOKEN" | npx wrangler secret put MOLTBOT_GATEWAY_TOKEN

# Cloudflare Access (Required for Admin UI)
npx wrangler secret put CF_ACCESS_TEAM_DOMAIN
# Enter your team domain (e.g., "myteam")

npx wrangler secret put CF_ACCESS_AUD
# Enter the Application Audience (AUD) from Access settings

# R2 Storage (Optional but Recommended)
npx wrangler secret put R2_ACCESS_KEY_ID
npx wrangler secret put R2_SECRET_ACCESS_KEY
npx wrangler secret put CF_ACCOUNT_ID
```

### 3. Deploy

```bash
npm run deploy
```

### 4. Access Admin UI

```
https://your-worker.workers.dev/?token=YOUR_GATEWAY_TOKEN
```

Replace:
- `your-worker` with your actual worker subdomain
- `YOUR_GATEWAY_TOKEN` with the token from step 2

### 5. Enable Cloudflare Access

After first deploy:
1. Go to Workers & Pages dashboard
2. Select your worker
3. Settings > Domains & Routes > Enable Cloudflare Access
4. Configure access policies

### 6. Pair Your Device

1. Go to `/_admin/` in your worker URL
2. Approve your device for pairing
3. Start using the Control UI!

## Model Configuration

The worker is pre-configured to use:
- **Provider**: OpenRouter
- **Model**: `moonshotai/kimi-k2.5`
- **Context Window**: 262,144 tokens

This is automatically configured when you set `AI_GATEWAY_BASE_URL` ending with `/openrouter`.

## Troubleshooting

**First request takes 1-2 minutes?** Normal - cold start.

**Access denied?** Make sure Cloudflare Access is enabled and secrets are set.

**AI not working?** Verify AI Gateway URL ends with `/openrouter` and OpenRouter API key is valid.

See [SETUP.md](./SETUP.md) for detailed troubleshooting.
