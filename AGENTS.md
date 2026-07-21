# AGENTS.md

## Cursor Cloud specific instructions

This repo is a small PoC: an n8n workflow (`workflow-triagem.json`) that calls OpenAI to
act as a clinic triage receptionist. There is no application source code, and no
lint/test/build steps — the "application" is the n8n server running the imported workflow.
The user-facing setup steps are documented in `README.md` (it uses Docker; in this cloud
environment we run n8n via npm instead, see below).

### Node version (important gotcha)
n8n 2.x requires Node.js `>=22.22`. The default `node` on `PATH` here is `/exec-daemon/node`
(v22.14.0), which n8n rejects at startup with "Your Node.js version ... is currently not
supported". A compatible Node is installed via nvm (`v22.22.2`). Before running n8n, put it
first on `PATH`:

```
export PATH="$HOME/.nvm/versions/node/v22.22.2/bin:$PATH"
```

`npm install` works with either node, so only the run step needs this.

### Running n8n (the app)
Dependencies (n8n) are installed locally via `npm install` (see `package.json`). Start it with:

```
export PATH="$HOME/.nvm/versions/node/v22.22.2/bin:$PATH"
export N8N_SECURE_COOKIE=false N8N_PORT=5678 N8N_HOST=0.0.0.0 GENERIC_TIMEZONE=UTC
npm run dev        # = n8n start
```

- Editor/UI: http://localhost:5678
- `N8N_SECURE_COOKIE=false` is required to log in over plain http in this environment.
- On first launch n8n asks you to create an owner account. Data is stored in `~/.n8n`
  (SQLite), outside the repo. Deleting `~/.n8n` resets the instance (owner + workflows).

### Loading and running the workflow
Import `workflow-triagem.json` in the UI via the canvas "⋯" menu → "Import from file...".
It has 3 nodes: `When chat message received` → `Message a model` (OpenAI, `gpt-4o-mini`) →
`Edit Fields`. To actually run the chat, the `Message a model` node needs a valid OpenAI
credential: create an "OpenAI" credential and paste an API key (expected as the
`OPENAI_API_KEY` secret). Without a key the node cannot return a response. Use the "Open
chat" button on the canvas to talk to the assistant.

Note: the workflow's node type versions require a recent n8n (2.x). Older 1.x n8n reports
the OpenAI node as "not installed" — keep n8n on the 2.x line pinned in `package.json`.
