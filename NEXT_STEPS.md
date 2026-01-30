# Next Steps - After Workers Paid Plan

You've upgraded to Workers Paid Plan! Here's what to do next:

## Step 1: Login to Cloudflare CLI

First, authenticate with Cloudflare:

```bash
cd /Users/xanderevans/Documents/XE:molt
npx wrangler login
```

This will open a browser window to authorize the CLI.

## Step 2: Get Your Cloudflare Account ID

1. Go to https://dash.cloudflare.com
2. Click the three dots menu (⋯) next to your account name (top right)
3. Click "Copy Account ID"
4. **Save this** - you'll need it later for R2 and AI Gateway

## Step 3: Set Up OpenRouter Account

1. Go to https://openrouter.ai and sign up/login
2. Add credits:
   - Go to https://openrouter.ai/credits
   - Add at least $10-20 (recommended)
3. Generate API key:
   - Go to https://openrouter.ai/keys
   - Click "Create Key"
   - **Copy and save the key** - you'll need it in Step 5

## Step 4: Set Up Cloudflare AI Gateway

1. Go to AI Gateway dashboard:
   https://dash.cloudflare.com/?to=/:account/ai/ai-gateway/create-gateway

2. Click "Create Gateway"
   - Name it: `moltworker-gateway` (or any name you prefer)
   - Click "Create"

3. Add OpenRouter Provider:
   - In your gateway, click "Add Provider"
   - Select "OpenRouter" from the list
   - Paste your OpenRouter API key (from Step 3)
   - Click "Save"

4. Get Gateway Endpoint URL:
   - In the gateway Overview tab, scroll to bottom
   - Expand "Native API/SDK Examples"
   - Select "OpenRouter"
   - **Copy the base URL** - it looks like:
     `https://gateway.ai.cloudflare.com/v1/{account_id}/{gateway_id}/openrouter`
   - **Save this URL** - you'll need it in Step 5

## Step 5: Set Up R2 Storage (Optional but Recommended)

R2 keeps your data persistent across container restarts.

1. Create R2 API Token:
   - Go to https://dash.cloudflare.com/?to=/:account/r2
   - Click "Manage R2 API Tokens"
   - Click "Create API Token"
   - Name it: `moltworker-r2-token`
   - Permissions: "Object Read & Write"
   - Bucket: `moltbot-data` (will be created on first deploy, or create it manually)
   - Click "Create API Token"
   - **IMPORTANT**: Copy both the Access Key ID and Secret Access Key immediately!

2. The bucket `moltbot-data` will be created automatically on first deploy, or you can create it manually in the R2 dashboard.

## Step 6: Configure Worker Secrets

Now set all the required secrets. Run these commands one by one:

```bash
# Make sure you're in the project directory
cd /Users/xanderevans/Documents/XE:molt

# 1. OpenRouter API Key (from Step 3)
npx wrangler secret put AI_GATEWAY_API_KEY
# Paste your OpenRouter API key when prompted

# 2. AI Gateway Base URL (from Step 4)
npx wrangler secret put AI_GATEWAY_BASE_URL
# Paste the AI Gateway endpoint URL (ends with /openrouter)

# 3. Generate Gateway Token
export MOLTBOT_GATEWAY_TOKEN=$(openssl rand -hex 32)
echo "🔑 YOUR GATEWAY TOKEN (SAVE THIS!): $MOLTBOT_GATEWAY_TOKEN"
echo "$MOLTBOT_GATEWAY_TOKEN" | npx wrangler secret put MOLTBOT_GATEWAY_TOKEN

# 4. Cloudflare Access Team Domain
# First, get your team domain:
# - Go to https://one.dash.cloudflare.com/
# - Settings > Custom Pages
# - Your team domain is the subdomain (e.g., "myteam" from "myteam.cloudflareaccess.com")
npx wrangler secret put CF_ACCESS_TEAM_DOMAIN
# Enter your team domain (just the subdomain part, e.g., "myteam")

# 5. Cloudflare Access AUD (we'll set this after deployment)
# For now, skip this - we'll do it in Step 8

# 6. R2 Storage (if you set it up in Step 5)
npx wrangler secret put R2_ACCESS_KEY_ID
# Paste your R2 Access Key ID

npx wrangler secret put R2_SECRET_ACCESS_KEY
# Paste your R2 Secret Access Key

npx wrangler secret put CF_ACCOUNT_ID
# Paste your Cloudflare Account ID (from Step 2)
```

## Step 7: Deploy the Worker

```bash
npm run deploy
```

This will:
- Build the worker
- Deploy to Cloudflare
- Show you your worker URL (e.g., `moltbot-sandbox.your-subdomain.workers.dev`)

**Save your worker URL!**

## Step 8: Set Up Cloudflare Access

After deployment:

1. Go to Workers & Pages dashboard:
   https://dash.cloudflare.com/?to=/:account/workers-and-pages

2. Click on your worker (e.g., `moltbot-sandbox`)

3. Go to **Settings** tab

4. Under **Domains & Routes**, find the `workers.dev` row

5. Click the three dots menu (⋯) and select **Enable Cloudflare Access**

6. Click **Manage Cloudflare Access** to configure:
   - Add your email address to the allow list
   - Or configure other identity providers (Google, GitHub, etc.)

7. **Copy the Application Audience (AUD) tag**:
   - In the Access application settings, find the "Application Audience (AUD)" field
   - Copy this value

8. Set the AUD secret:
   ```bash
   npx wrangler secret put CF_ACCESS_AUD
   # Paste the AUD value you just copied
   ```

9. Redeploy:
   ```bash
   npm run deploy
   ```

## Step 9: Access the Admin UI

1. Open your browser to:
   ```
   https://your-worker.workers.dev/?token=YOUR_GATEWAY_TOKEN
   ```
   Replace:
   - `your-worker` with your actual worker subdomain
   - `YOUR_GATEWAY_TOKEN` with the token from Step 6

2. **First request takes 1-2 minutes** (cold start) - be patient!

3. You'll be prompted to authenticate via Cloudflare Access

## Step 10: Pair Your Device

1. After the Control UI loads, go to:
   ```
   https://your-worker.workers.dev/_admin/
   ```

2. You'll see pending device pairing requests

3. Approve your device

4. Start using your AI assistant!

## Verify Everything Works

1. **Check Model**: Verify MoonshotAI Kimi K2.5 is available in the Control UI
2. **Test AI**: Send a test message and verify it responds
3. **Check R2** (if configured): In Admin UI, look for "Last backup: [timestamp]"

## Quick Commands Reference

```bash
# Check Worker logs
npx wrangler tail

# List all secrets
npx wrangler secret list

# View deployments
npx wrangler deployments list
```

## Need Help?

- See [SETUP.md](./SETUP.md) for detailed instructions
- See [DEPLOYMENT_CHECKLIST.md](./DEPLOYMENT_CHECKLIST.md) for a checklist
- Check Worker logs: `npx wrangler tail`
