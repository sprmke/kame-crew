# Orca worktrees

Each Orca worktree runs its own Next server on a sticky port and shares the
configured Convex deployment.

## Ports

| Checkout             | Port                 |
| -------------------- | -------------------- |
| Primary checkout     | `3000`               |
| Other Orca worktrees | Sticky `3400`-`3499` |

```bash
bun run ui          # this worktree's Next server, writes .local/vite-port
bun run ui:port     # print the port only
```

A collision bumps to the next free port and never kills another worktree's
listener.

## Orca setup

Repo root [`orca.yaml`](../orca.yaml):

- `scripts.setup` → `bun install` + copy `.env.local` from `ORCA_ROOT_PATH` when missing
- `defaultTabs` → opens a **UI** tab that runs `bun run ui`
- `scripts.archive` → stops only this worktree's Next server

In Orca: **Settings → Projects → kame-crew → Worktree hooks → Advanced →
Command source** → **orca.yaml only**.

## Local test login

`/sign-in` shows **Use local test account** when the app runs with
`next dev`:

- It calls the existing `users.CreateUser` mutation with
  `local-test@kame.test` and opens `/assistants`
- The user is created in the Convex deployment from
  `NEXT_PUBLIC_CONVEX_URL`, so only use the button against a dev deployment
- The button never renders in a production build

Google sign-in remains the normal path. Use the button when an embedded browser
cannot complete Google OAuth.

## Verify

1. `bun run ui` → open the printed URL.
2. Go to `/sign-in`, click **Use local test account**.
3. The app lands on `/assistants` with the local test user.
