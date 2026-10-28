# CLAUDE.md

FiveM/ESX scrapyard suite "Schrotthandel". Resources live under `[wr_scrap]/` with prefix `wr_scrap_*`.

## Read first
- `docs/ENTSCHEIDUNGEN.md`: binding design decisions (do not deviate silently; record changes there)
- `docs/KONZEPT.md`: gameplay spec
- `docs/ARCHITEKTUR.md`: resources, bridges, data model, sync, conventions
- `docs/ROADMAP.md`: building blocks (Bausteine) and their status

## Rules
- Work one roadmap block at a time; update its status in `docs/ROADMAP.md` and add `docs/tests/<id>.md`.
- Push directly to `main` (the owner's explicit decision).
- Must run standalone on ESX Legacy + ox_lib + ox_inventory + ox_target + oxmysql (+ OneSync).
  Everything else goes through a bridge adapter in `wr_scrap_core/bridge/` with a working fallback.
- Use ox_lib for Lua utilities only; all UI is our own NUI in `wr_scrap_ui` (React + Vite + TS). Commit the built `dist/`.
- Server-authoritative: the client sends intents, the server validates (distance, tool, dependencies, lock, min duration, token).
- Code, identifiers and comments in English; docs and config explanations in German; all ingame text via locales (`de`, `en`).
- DB tables prefixed `wr_`, schema changes only via additive migrations in `migrations/`.
- Items come from the single catalog in `wr_scrap_core/shared/catalog/`; never hardcode item names, use catalog IDs + alias mapping.
- No third-party models/assets without a clearly permissive license.
- The illegal side must never surface labels or menus that reveal it; it is gated by knowledge, not permissions.
- No automatic police alerts or fines; only traces that investigators can find.
- Ingame testing is not possible here: provide debug commands, a dry-run mode for destructive DB operations, and a test checklist.
