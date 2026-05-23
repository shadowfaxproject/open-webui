---
name: project-rebase-workflow
description: "Workflow for rebasing open-webui-custom fork to upstream and re-applying Magicbox custom patches — remotes, branch conventions, key conflict files, and patch order"
metadata: 
  node_type: memory
  type: project
  originSessionId: 4f310a68-7625-4d6f-8f10-3f63c48bd93f
---

The open-webui-custom repo (`shadowfaxproject/open-webui`) is a fork of `open-webui/open-webui` (upstream). Periodically it is rebased onto `upstream/main` and custom Magicbox patches are re-applied.

**Why:** Upstream moves fast; a rebase (not merge) keeps history clean and custom commits isolated at the tip.

**How to apply:** Run `/rebase-and-patch` skill for step-by-step guidance.

## Key facts

- Working directory: `/Users/vivektawde/local/code/open-webui-custom`
- Origin: `https://github.com/shadowfaxproject/open-webui.git`
- Upstream: `https://github.com/open-webui/open-webui.git` (push disabled)
- Branch convention: `sync-wip` during work → rename to `sync-complete` when done
- Backup before rebase: `sync-wip-backup-pre-rebase`

## Custom patches (applied in this order after every rebase)

1. `ResponseMessage.svelte` — ContentRenderer props (`messageId`, `history`, `selectedModels`), ProductGrid block, regenerate menu block structure
2. `Sidebar.svelte` + `Sidebar/UserMenu.svelte` — ChevronLeft/Right icons, remove UserMenu from bottom
3. `layout/Navbar.svelte`, `chat/Navbar.svelte`, `channel/Navbar.svelte` — remove toggle sidebar buttons
4. `src/routes/+layout.svelte` — keep WEBUI_NAME as Magicbox (don't overwrite from backend)
5. `backend/routers/tasks.py`, `utils/task.py`, `utils/middleware.py` — gift_request_desc for title generation
6. `ProductDetailsModal.svelte` — Modal portal for viewport centering, 400×400 size
7. `MessageInput.svelte` — inline single-row input, remove + and tool buttons, slim padding

## Common post-rebase build errors

- **"Unexpected block closing tag"** in ResponseMessage.svelte → mismatched `{#if}/{/if}` from conflict resolution
- **ProductGrid not rendering** → ContentRenderer missing `messageId`, `history`, `selectedModels` props
- **ProductDetailsModal off-screen** → `position:fixed` broken by CSS transform ancestor; fix with Modal.svelte portal

**How to apply:** [[project-sync-status]]
