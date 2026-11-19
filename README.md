# iMagnate OpenAI agent

Private relay for the price workbench. It is deployed separately from Strapi in a supported region and is not a user-facing service.

## Render setup

1. Create a **Web Service** from this repository in region **Frankfurt**. Leave the root-directory field empty: this repository contains only the agent.
2. Set `OPENAI_API_KEY` and a new long random `AI_AGENT_TOKEN` as Render secret environment variables. Never commit either value.
3. Keep the model at `gpt-6-luna` until its API availability has been checked from this service. The price-workbench AI configuration must use the same model name.
4. Open `/health`, then call authenticated `/v1/preflight` from staging. Only after it returns `ready: true` enable the AI-helper test branch.

The only public unauthenticated endpoint is `/health`. Both `/v1/preflight` and `/v1/responses` require `x-imagnate-agent-token`. The service accepts only the pinned price-workbench extraction request, has a 120 KB request limit, a one-minute rate limit, and does not log price rows or either secret. It checks the model JSON again before returning it: a suggestion can cite only submitted evidence and can select only a card that Strapi offered as a candidate.

Confirmed mappings remain in the iMagnate database. They are the durable learning memory: after an employee confirms a match, the program uses it on future lists; the agent never stores unconfirmed supplier prices or applies a price itself. This avoids losing training data on a Render restart and keeps the catalogue as the single source of truth.
