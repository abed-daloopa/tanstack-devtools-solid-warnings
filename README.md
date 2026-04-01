# TanStack Devtools — Solid.js Warnings in React Apps (Vite 8)

Minimal reproduction for Solid.js warnings appearing in the browser console of a **React** TanStack Start application when using TanStack Devtools with **Vite 8**.

## The Problem

When using `@tanstack/devtools-vite` with devtools plugins in a Vite 8 project, the following warnings flood the browser console:

```
You appear to have multiple instances of Solid. This can lead to unexpected behavior.
computations created outside a `createRoot` or `render` will never be disposed
cleanups created outside a `createRoot` or `render` will never be run
```

These warnings do **not** appear with Vite 7 using the same dependencies and configuration.

## Root Cause

TanStack Devtools uses Solid.js internally for its UI. Vite 8 (powered by Rolldown) changed how dependency optimization and module resolution work, causing Solid.js to be loaded as multiple instances at runtime — even when only a single copy exists in `node_modules`.

In Vite 7, the dependency optimizer (esbuild-based) correctly deduplicates Solid.js into a single instance. Vite 8's Rolldown-based optimizer does not, resulting in the devtools packages each getting their own Solid.js instance.

Adding `resolve.dedupe: ["solid-js"]` to the Vite config does not fix the issue.

## Steps to Reproduce

```bash
bun install
bun run dev
```

Open http://localhost:3000 and check the browser console (or the terminal — Vite 8 forwards client `console.warn` calls).

### To verify Vite 8 is the cause

Downgrade to Vite 7 in `package.json`:

```diff
- "vite": "^8.0.3"
+ "vite": "^7.3.1"
```

Reinstall and run — the warnings disappear.

## Environment

- Vite 8.0.3
- TanStack Start (React, SPA mode)
- Bun
- `@tanstack/devtools-vite`
- `@tanstack/react-devtools`
- `@tanstack/react-form-devtools`
- `@tanstack/react-pacer-devtools`
- `@tanstack/react-hotkeys-devtools`
- `@tanstack/react-query-devtools`
