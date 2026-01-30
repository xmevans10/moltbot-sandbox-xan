# Moltworker Setup Guide - MoonshotAI Kimi K2.5 via OpenRouter

This guide will walk you through setting up Moltworker (OpenClaw on Cloudflare Workers) with MoonshotAI Kimi K2.5 via OpenRouter.

## Prerequisites

- Node.js 18+ installed
- A Cloudflare account
- An OpenRouter account

## Step 1: Cloudflare Account Setup

1. **Create Cloudflare Account** (if you don't have one)
   - Go to https://dash.cloudflare.com/sign-up
   - Sign up for a free account

2. **Upgrade to Workers Paid Plan**
   - Go to [Workers & Pages Dashboard](https://dash.cloudflare.com/?to=/:account/workers/plans)
   - Upgrade to the **Workers Paid Plan** ($5 USD/month minimum)
   - This is required for Cloudflare Sandbox Containers

3. **Enable Containers**
   - Go to [Containers Dashboard](https://dash.cloudflare.com/?to=/:account/workers/containers)
   - Ensure containers are enabled for your account

4. **Get Your Account ID**
   - In the Cloudflare Dashboard, click the three dots menu next to your account name
   - Select "Copy Account ID"
   - Save this for later use

## Step 2: OpenRouter Account Setup

1. **Create OpenRouter Account**
   - Go to https://openrouter.ai/
   - Sign up for an account

2. **Add Credits**
   - Navigate to https://openrouter.ai/credits
   - Add credits to your account (minimum recommended: $10-20)

3. **Generate API Key**
   - Go to https://openrouter.ai/keys
   - Click "Create Key"
   - Copy and save your API key securely
   - You'll need this for AI Gateway configuration

## Step 3: Cloudflare AI Gateway Setup

1. **Create AI Gateway**
   - Go to [AI Gateway Dashboard](https://dash.cloudflare.com/?to=/:account/ai/ai-gateway/create-gateway)
   - Click "Create Gateway"
   - Give it a name (e.g., "moltworker-gateway")
   - Click "Create"

2. **Add OpenRouter Provider**
   - In your gateway, click "Add Provider"
   - Select "OpenRouter" from the list
   - Enter your OpenRouter API key
   - Click "Save"

3. **Get Gateway Endpoint URL**
   - In the gateway Overview tab, scroll to the bottom
   - Expand the "Native API/SDK Examples" section
   - Select "OpenRouter"
   - Copy the base URL (format: `https://gateway.ai.cloudflare.com/v1/{account_id}/{gateway_id}/openrouter`)
   - Save this URL for the next step

## Step 4: R2 Storage Setup (Optional but Recommended)

R2 storage enables persistent storage across container restarts, preserving your paired devices and conversation history.

1. **Create R2 Bucket**
   - The bucket `moltbot-data` will be created automatically on first deploy
   - Or manually create it:
     - Go to [R2 Dashboard](https://dash.cloudflare.com/?to=/:account/r2)
     - Click "Create bucket"
     - Name it `moltbot-data`
     - Click "Create"

2. **Create R2 API Token**
   - In R2 Dashboard, click "Manage R2 API Tokens"
   - Click "Create API Token"
   - Name it (e.g., "moltworker-r2-token")
   - Select "Object Read & Write" permissions
   - Select the `moltbot-data` bucket
   - Click "Create API Token"
   - **IMPORTANT**: Copy both the Access Key ID and Secret Access Key immediately (you won't see the secret again)

## Step 5: Zero Trust Access Setup

1. **Enable Cloudflare Access on workers.dev** (Easiest method)
   - After deploying (Step 7), go to [Workers & Pages Dashboard](https://dash.cloudflare.com/?to=/:account/workers-and-pages)
   - Select your Worker (e.g., `moltbot-sandbox`)
   - In **Settings**, under **Domains & Routes**, find the `workers.dev` row
   - Click the meatballs menu (`...`)
   - Click **Enable Cloudflare Access**
   - Click **Manage Cloudflare Access** to configure access:
     - Add your email address to the allow list
     - Or configure other identity providers (Google, GitHub, etc.)
   - Copy the **Application Audience (AUD)** tag from the Access application settings
   - Save this AUD value for Step 6

2. **Get Your Team Domain**
   - Go to [Zero Trust Dashboard](https://one.dash.cloudflare.com/)
   - Navigate to **Settings** > **Custom Pages**
   - Your team domain is the subdomain before `.cloudflareaccess.com` (e.g., `myteam`)
   - Save this for Step 6

## Step 6: Configure Worker Secrets

Run these commands in the project directory to set up all required secrets:

```bash
# OpenRouter API Key (for AI Gateway)
npx wrangler secret put AI_GATEWAY_API_KEY
# Paste your OpenRouter API key when prompted

# AI Gateway Base URL
npx wrangler secret put AI_GATEWAY_BASE_URL
# Paste your AI Gateway endpoint URL (from Step 3)
# Format: https://gateway.ai.cloudflare.com/v1/{account_id}/{gateway_id}/openrouter

# Generate and set Gateway Token (required for remote access)
export MOLTBOT_GATEWAY_TOKEN=$(openssl rand -hex 32)
echo "Your gateway token: $MOLTBOT_GATEWAY_TOKEN"
echo "$MOLTBOT_GATEWAY_TOKEN" | npx wrangler secret put MOLTBOT_GATEWAY_TOKEN
# Save the token displayed above - you'll need it to access the Control UI

# Cloudflare Access Configuration (for Admin UI)
npx wrangler secret put CF_ACCESS_TEAM_DOMAIN
# Enter your team domain (e.g., "myteam" from Step 5)

npx wrangler secret put CF_ACCESS_AUD
# Enter the Application Audience (AUD) tag from Step 5

# R2 Storage Configuration (optional but recommended)
npx wrangler secret put R2_ACCESS_KEY_ID
# Paste your R2 Access Key ID from Step 4

npx wrangler secret put R2_SECRET_ACCESS_KEY
# Paste your R2 Secret Access Key from Step 4

npx wrangler secret put CF_ACCOUNT_ID
# Paste your Cloudflare Account ID from Step 1
```

## Step 7: Deploy the Worker

1. **Build and Deploy**
   ```bash
   npm run deploy
   ```

2. **Note Your Worker URL**
   - After deployment, note your worker URL (e.g., `moltbot-sandbox.your-subdomain.workers.dev`)
   - Save this URL

## Step 8: Access the Admin UI

1. **Access the Control UI**
   - Open: `https://your-worker.workers.dev/?token=YOUR_GATEWAY_TOKEN`
   - Replace `your-worker` with your actual worker subdomain
   - Replace `YOUR_GATEWAY_TOKEN` with the token you generated in Step 6

2. **First Request**
   - The first request may take 1-2 minutes while the container starts
   - Be patient and wait for the page to load

3. **Pair Your Device**
   - Once the Admin UI loads, go to `/_admin/`
   - You'll need to approve your device for pairing
   - After approval, you can start using the Control UI

## Step 9: Verify Configuration

1. **Check Model Configuration**
   - In the Control UI, verify that MoonshotAI Kimi K2.5 is available
   - The model should be set as the primary model: `openrouter/moonshotai/kimi-k2.5`

2. **Test R2 Storage** (if configured)
   - In the Admin UI, check for "Last backup: [timestamp]"
   - Click "Backup Now" to trigger an immediate sync
   - Verify the backup completes successfully

3. **Test AI Functionality**
   - Send a test message through the Control UI
   - Verify that the AI responds using the Kimi K2.5 model

## Troubleshooting

### Container fails to start
- Check Worker logs: `npx wrangler tail`
- Verify all secrets are set: `npx wrangler secret list`
- Ensure Workers Paid Plan is active

### AI Gateway not working
- Verify AI Gateway endpoint URL is correct
- Check that OpenRouter API key is valid and has credits
- Verify the gateway endpoint ends with `/openrouter`

### R2 not mounting
- Ensure all three R2 secrets are set: `R2_ACCESS_KEY_ID`, `R2_SECRET_ACCESS_KEY`, `CF_ACCOUNT_ID`
- Note: R2 mounting only works in production, not with `wrangler dev`

### Access denied on admin routes
- Verify `CF_ACCESS_TEAM_DOMAIN` and `CF_ACCESS_AUD` are set correctly
- Check that Cloudflare Access is enabled on your worker
- Ensure your email is in the Access allow list

### Slow first request
- Cold starts take 1-2 minutes - this is normal
- Subsequent requests are much faster

## Next Steps

- Add chat integrations (Slack, Discord, Telegram) - see README.md
- Configure additional skills/plugins
- Set up monitoring and alerting
- Customize the Admin UI if needed

## Cost Estimates

- **Workers Paid Plan**: $5/month (required for Sandboxes)
- **AI Gateway**: Free tier available, then pay-as-you-go
- **R2 Storage**: Free tier (10 GB storage, 1M Class A operations/month)
- **Browser Rendering**: Free tier available
- **OpenRouter**: Pay-per-use based on model usage (Kimi K2.5: $0.50/M input tokens, $2.80/M output tokens)

## Support

- Moltworker GitHub: https://github.com/cloudflare/moltworker
- OpenClaw Documentation: https://docs.openclaw.ai/
- Cloudflare Sandbox Docs: https://developers.cloudflare.com/sandbox/
