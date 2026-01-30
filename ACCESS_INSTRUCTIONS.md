# How to Access Your Moltbot

## Your Worker is Deployed! 🎉

Your Moltbot worker is live at:
**https://moltbot-sandbox.{your-subdomain}.workers.dev**

## Step 1: Get Your Worker URL

1. Go to [Workers & Pages Dashboard](https://dash.cloudflare.com/?to=/:account/workers-and-pages)
2. Click on `moltbot-sandbox`
3. Your worker URL will be shown (e.g., `moltbot-sandbox.your-subdomain.workers.dev`)

## Step 2: Enable Cloudflare Access (Required)

Before you can access the Control UI, you need to enable Cloudflare Access:

1. In the Workers & Pages dashboard, select `moltbot-sandbox`
2. Go to **Settings** tab
3. Under **Domains & Routes**, find the `workers.dev` row
4. Click the three dots menu (⋯) and select **Enable Cloudflare Access**
5. Click **Manage Cloudflare Access** to configure:
   - Add your email address (`xmevans10@gmail.com`) to the allow list
   - Or configure other identity providers (Google, GitHub, etc.)
6. **Copy the Application Audience (AUD) tag** from the Access application settings
7. Set the AUD secret:
   ```bash
   cd /Users/xanderevans/Documents/XE:molt
   ./node_modules/.bin/wrangler secret put CF_ACCESS_AUD
   # Paste the AUD value
   ```
8. Get your team domain:
   - Go to [Zero Trust Dashboard](https://one.dash.cloudflare.com/)
   - Settings > Custom Pages
   - Your team domain is the subdomain (e.g., "myteam" from "myteam.cloudflareaccess.com")
9. Set the team domain:
   ```bash
   ./node_modules/.bin/wrangler secret put CF_ACCESS_TEAM_DOMAIN
   # Enter your team domain (just the subdomain part)
   ```
10. Redeploy:
    ```bash
    npm run deploy
    ```

## Step 3: Access the Control UI

Once Cloudflare Access is configured:

1. **Control UI** (Main chat interface):
   ```
   https://moltbot-sandbox.{your-subdomain}.workers.dev/?token=IsL8AmJw5SsPUNNz7U22H3nmOjU4O-RFhXqw3m1X
   ```
   Replace `{your-subdomain}` with your actual subdomain.

2. **Admin UI** (Device management):
   ```
   https://moltbot-sandbox.{your-subdomain}.workers.dev/_admin/
   ```

## Step 4: Pair Your Device

1. When you first access the Control UI, your device will be pending approval
2. Go to the Admin UI at `/_admin/`
3. You'll see your device in the "Pending Devices" section
4. Click "Approve" to pair your device
5. Now you can use the Control UI!

## Important Notes

- **First request takes 1-2 minutes**: This is normal - the container needs to cold start
- **Gateway Token**: `IsL8AmJw5SsPUNNz7U22H3nmOjU4O-RFhXqw3m1X` (save this!)
- **Authentication**: You'll be prompted to authenticate via Cloudflare Access when accessing protected routes

## Troubleshooting

**Can't access?**
- Make sure Cloudflare Access is enabled
- Verify `CF_ACCESS_TEAM_DOMAIN` and `CF_ACCESS_AUD` secrets are set
- Check that your email is in the Access allow list

**Container not starting?**
- Check logs: `./node_modules/.bin/wrangler tail`
- Verify all secrets are set: `./node_modules/.bin/wrangler secret list`

**Slow first request?**
- Normal! Cold starts take 1-2 minutes
- Subsequent requests are much faster

## Quick Access Commands

```bash
# View logs
./node_modules/.bin/wrangler tail

# List secrets
./node_modules/.bin/wrangler secret list

# View deployments
./node_modules/.bin/wrangler deployments list
```
