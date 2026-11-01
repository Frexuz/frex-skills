---
name: fast-refresh-edit-order
description: >-
  Orders multi-step Expo and React Native edits so Metro Fast Refresh never
  sees a broken file (red error overlay, lost state, full reload). Use when
  editing apps/mobile or any Expo project while Metro is running, or when
  adding or removing imports, components, hooks, or cross-file symbols.
license: MIT
---

# Fast Refresh–safe edit order (Expo / React Native)

When Expo Metro is running, **each saved change to a file triggers Fast Refresh**. If the file is temporarily invalid (references a missing import, or code still runs against a symbol you removed), the dev client often shows the **red error overlay**, **resets React state**, or **full-reloads** so you are no longer on the screen you were testing.

Avoid that broken intermediate state.

## Prefer one save per file

When adding or removing a symbol, put **imports and usages in the same edit** so the file is valid before Metro reloads.

## When you must split edits on the same file

### Removing (component, hook, helper, dedicated const)

1. Remove **all usages** in JSX, hooks, and logic first.
2. Then remove the **import** and any **module-level consts/types** that only existed for that usage.

**Wrong:** delete the import first while `<Component />` or `foo()` still appears → compile/runtime error → red screen → extra reloads.

### Adding

1. Add **imports** (and any supporting module-level consts/types) first.
2. Then add **JSX, hook calls, and logic** that reference them.

**Wrong:** add JSX before the import → same broken intermediate state.

Unused imports alone are usually fine; **missing bindings for code that still runs** are not.

## Cross-file changes

When deleting or gutting a module:

1. Update **every consumer** (remove usages) first, or in the same batch before saving the provider file.
2. Then remove or slim down the **provider** file.

## Tooling (agents)

- Prefer **one `Write` / `StrReplace` per file** that leaves the file valid.
- If you already saved a broken state, **fix that file in the very next edit** — do not switch to other files first.

## Quick reference

| Change | Order |
| --- | --- |
| **Remove** | Usages in JSX/logic **first** → import and dedicated consts **last** |
| **Add** | Imports (and supporting consts) **first** → usages **second** |
