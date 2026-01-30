# OpenRouter Configuration Summary

This document summarizes the OpenRouter (MoonshotAI Kimi K2.5) configuration changes made to Moltworker.

## Changes Made

### 1. Environment Variable Handling (`src/gateway/env.ts`)

Added support for detecting OpenRouter gateway endpoints:
- Detects `/openrouter` suffix in `AI_GATEWAY_BASE_URL`
- Maps OpenRouter API key to `OPENAI_API_KEY` (OpenRouter uses OpenAI-compatible API)
- Sets `OPENAI_BASE_URL` when OpenRouter gateway is detected

### 2. Container Configuration (`start-moltbot.sh`)

Added OpenRouter provider configuration:
- Detects OpenRouter gateway endpoint
- Configures `openrouter` provider with base URL override
- Sets up MoonshotAI Kimi K2.5 model:
  - Model ID: `moonshotai/kimi-k2.5`
  - Display Name: `MoonshotAI Kimi K2.5`
  - Context Window: 262,144 tokens
- Sets primary model to `openrouter/moonshotai/kimi-k2.5`

### 3. Configuration Files

- Updated `wrangler.jsonc` comments to document OpenRouter/AI Gateway setup
- Created comprehensive setup documentation:
  - `SETUP.md` - Detailed step-by-step guide
  - `QUICK_START.md` - Condensed quick reference
  - `DEPLOYMENT_CHECKLIST.md` - Pre/post deployment checklist

## How It Works

1. **AI Gateway Setup**: User creates AI Gateway in Cloudflare and adds OpenRouter as a provider
2. **Endpoint Configuration**: AI Gateway provides endpoint URL ending with `/openrouter`
3. **Worker Configuration**: User sets `AI_GATEWAY_BASE_URL` and `AI_GATEWAY_API_KEY` secrets
4. **Container Detection**: `start-moltbot.sh` detects `/openrouter` suffix and configures OpenRouter provider
5. **Model Selection**: MoonshotAI Kimi K2.5 is automatically set as the primary model

## Model Details

- **Provider**: OpenRouter
- **Model**: `moonshotai/kimi-k2.5`
- **Context Window**: 262,144 tokens
- **Pricing**: $0.50/M input tokens, $2.80/M output tokens
- **Features**: Multimodal, strong reasoning, agentic tool-calling

## Usage

Once configured, the model is available in the Control UI as:
- Model ID: `openrouter/moonshotai/kimi-k2.5`
- Display Name: `Kimi K2.5`

The model is automatically set as the primary model, but users can change it in the Control UI if needed.

## Switching Models

To use a different OpenRouter model:
1. Edit `start-moltbot.sh`
2. Update the model configuration in the `isOpenRouter` block
3. Add the new model to the `models` array
4. Update the primary model selection
5. Redeploy the worker

## Troubleshooting

**Model not appearing?**
- Verify `AI_GATEWAY_BASE_URL` ends with `/openrouter`
- Check that OpenRouter API key is valid and has credits
- Review container logs: `npx wrangler tail`

**API errors?**
- Verify AI Gateway is configured correctly
- Check OpenRouter account has sufficient credits
- Ensure model ID matches OpenRouter's model catalog
