# Deployment Checklist

Use this checklist to ensure all steps are completed before deploying.

## Pre-Deployment

- [ ] Cloudflare account created
- [ ] Workers Paid Plan activated ($5/month)
- [ ] Containers enabled in Cloudflare dashboard
- [ ] Cloudflare Account ID copied and saved
- [ ] OpenRouter account created
- [ ] OpenRouter credits added (minimum $10-20 recommended)
- [ ] OpenRouter API key generated and saved
- [ ] AI Gateway created in Cloudflare
- [ ] OpenRouter provider added to AI Gateway
- [ ] AI Gateway endpoint URL copied (ends with `/openrouter`)
- [ ] R2 bucket `moltbot-data` will be created automatically (or create manually)
- [ ] R2 API token created (Access Key ID and Secret Access Key saved)

## Secrets Configuration

Run these commands and verify each secret is set:

- [ ] `AI_GATEWAY_API_KEY` - OpenRouter API key
- [ ] `AI_GATEWAY_BASE_URL` - AI Gateway endpoint URL
- [ ] `MOLTBOT_GATEWAY_TOKEN` - Generated token (save this!)
- [ ] `CF_ACCESS_TEAM_DOMAIN` - Cloudflare Access team domain
- [ ] `CF_ACCESS_AUD` - Application Audience (AUD) tag
- [ ] `R2_ACCESS_KEY_ID` - R2 access key (optional but recommended)
- [ ] `R2_SECRET_ACCESS_KEY` - R2 secret key (optional but recommended)
- [ ] `CF_ACCOUNT_ID` - Cloudflare account ID (optional but recommended)

Verify secrets:
```bash
npx wrangler secret list
```

## Deployment

- [ ] Dependencies installed: `npm install`
- [ ] Build successful: `npm run build`
- [ ] Deploy command ready: `npm run deploy`
- [ ] Worker URL noted after deployment

## Post-Deployment

- [ ] Cloudflare Access enabled on workers.dev route
- [ ] Access policies configured (email allow list or identity providers)
- [ ] Admin UI accessible: `https://your-worker.workers.dev/?token=YOUR_TOKEN`
- [ ] Device paired via `/_admin/` endpoint
- [ ] Model verified: MoonshotAI Kimi K2.5 available
- [ ] R2 backup tested (if configured)
- [ ] Test message sent and AI responded

## Verification Commands

```bash
# Check Worker logs
npx wrangler tail

# List all secrets
npx wrangler secret list

# Check Worker status
npx wrangler deployments list
```

## Troubleshooting Quick Reference

| Issue | Solution |
|-------|----------|
| Container fails to start | Check logs, verify Paid Plan active |
| Access denied | Verify Access secrets and policies |
| AI not working | Check AI Gateway URL ends with `/openrouter` |
| R2 not mounting | Verify all 3 R2 secrets are set |
| Slow first request | Normal (1-2 min cold start) |

## Next Steps After Deployment

- [ ] Add chat integrations (Slack/Discord/Telegram) if desired
- [ ] Configure additional skills/plugins
- [ ] Set up monitoring
- [ ] Customize Admin UI if needed
