# Rebase to Upstream and Apply Custom Patches

Guide for syncing the `shadowfaxproject/open-webui` fork with `open-webui/open-webui` upstream and re-applying Magicbox custom patches.

## Repo layout

- **Fork (origin):** `https://github.com/shadowfaxproject/open-webui.git`
- **Upstream:** `https://github.com/open-webui/open-webui.git`
- **Custom patches branch:** `sync-complete` (or similar `sync-*` name)
- **Backup branch before rebase:** `sync-wip-backup-pre-rebase`

## Step 1 — Fetch upstream

```bash
git fetch upstream
```

## Step 2 — Create a backup of the current working branch

```bash
git checkout -b sync-wip-backup-pre-rebase
git checkout sync-wip   # or whatever the current patch branch is
```

## Step 3 — Rebase onto upstream/main

```bash
git rebase upstream/main
```

Resolve conflicts as they arise. Key conflict areas in this repo:

| File | Typical conflict |
|------|-----------------|
| `src/lib/components/chat/Messages/ResponseMessage.svelte` | Svelte block tags (`{#if}/{:else}/{/if}`) get misaligned — check nesting carefully after rebase |
| `src/lib/components/layout/Sidebar.svelte` | Large file; upstream changes frequently |
| `backend/open_webui/routers/tasks.py` | Title generation logic |
| `backend/open_webui/utils/task.py` | `title_generation_template` signature |
| `backend/open_webui/utils/middleware.py` | Title string parsing |

After each conflict file: `git add <file> && git rebase --continue`

## Step 4 — Verify build passes

```bash
npm run build
```

Fix any Svelte compile errors before proceeding. Common post-rebase issues:
- **"Unexpected block closing tag"** — mismatched `{#if}/{/if}` blocks in ResponseMessage.svelte
- **Missing props on ContentRenderer** — `messageId`, `history`, `selectedModels` must be passed

## Step 5 — Re-apply custom patches (if lost in rebase)

Apply in this logical order. Check each file against the backup branch (`sync-wip-backup-pre-rebase`) if needed.

### 1. ResponseMessage.svelte
- Add `messageId={message.id}`, `{history}`, `{selectedModels}` props to `<ContentRenderer>`
- Keep custom ProductGrid rendering block: `{:else if message.content.includes('"product_list":')}` → `<ProductGrid chat_id={chatId} ...>`
- Regenerate button: hidden (`class="hidden regenerate-response-button"`) with `<RegenerateMenu>` component

### 2. Sidebar.svelte
- Close button icon: `<ChevronLeft className="size-5" />`
- Open strip hover icon: `<ChevronRight className="size-5" />`
- Remove `UserMenu` block from bottom of both open and collapsed sidebar states

### 3. Navbars — remove toggle sidebar buttons
- `src/lib/components/layout/Navbar.svelte`
- `src/lib/components/chat/Navbar.svelte`
- `src/lib/components/channel/Navbar.svelte`

### 4. App name — `src/routes/+layout.svelte`
- Comment out `await WEBUI_NAME.set(backendConfig.name)` to preserve `APP_NAME = 'Magicbox'`

### 5. Backend title generation
- `tasks.py`: use `chat_state.gift_request.describe()` → `title_generation_template(template, gift_request_desc, user)`
- `task.py`: `title_generation_template(template, gift_request_desc, user)` — set `prompt = gift_request_desc`
- `middleware.py`: set `title = title_string` directly (no JSON parsing)

### 6. ProductDetailsModal.svelte
- Use `<Modal bind:show size="sm" ...>` (portals to `document.body` for correct viewport centering)
- Inner div: `class="relative h-[400px] w-full"`
- Script: `let show = true; $: if (!show) dispatch('close');`

### 7. MessageInput.svelte
- Remove `InputMenu`, `IntegrationsMenu`, valves, tool/filter toggles, create-note, terminal menu
- Single flex row: `<div class="flex justify-between mt-1 mb-1 mx-0.5 max-w-full" dir="ltr">`
- Inner: `<div class="ml-1 self-end flex items-center flex-1 gap-0.5">`
- Text input gets `flex-1` (not `w-full`) — sits inline with dictate and send buttons

## Step 6 — Commit as logical units

```bash
git add src/lib/components/chat/Messages/ResponseMessage.svelte
git commit -m "fix(ResponseMessage): restore ContentRenderer props and block structure after rebase"

git add src/lib/components/layout/Sidebar.svelte src/lib/components/layout/Sidebar/UserMenu.svelte
git commit -m "feat(sidebar): replace toggle icons with ChevronLeft/Right; remove user profile"

git add src/lib/components/layout/Navbar.svelte src/lib/components/chat/Navbar.svelte src/lib/components/channel/Navbar.svelte
git commit -m "feat(navbar): remove redundant sidebar toggle buttons from all navbars"

git add src/routes/+layout.svelte
git commit -m "fix(app): keep app name as Magicbox instead of backend config name"

git add backend/open_webui/routers/tasks.py backend/open_webui/utils/task.py backend/open_webui/utils/middleware.py
git commit -m "feat(backend): restore gift_request_desc for automatic title generation"

git add src/lib/components/common/ProductDetailsModal.svelte
git commit -m "feat(ProductDetailsModal): center on viewport; set 400x400 size"

git add src/lib/components/chat/MessageInput.svelte
git commit -m "feat(MessageInput): inline input with send/dictate buttons; remove + and tool buttons"
```

## Step 7 — Rename branch and push

```bash
git branch -m sync-wip sync-complete
git push origin sync-complete
```
