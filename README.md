# TanStack Devtools — Solid.js Warnings in React Apps

Minimal reproduction for Solid.js warnings appearing in the browser console of a **React** TanStack Start application when using TanStack Devtools.

## The Problem

When using `@tanstack/devtools-vite` with multiple devtools packages (`@tanstack/react-devtools`, `@tanstack/react-form-devtools`, `@tanstack/react-pacer-devtools`, `@tanstack/react-hotkeys-devtools`), the following warnings appear in the **browser console**:

```sh
[console.warn] You appear to have multiple instances of Solid. This can lead to unexpected behavior.
[console.warn] computations created outside a `createRoot` or `render` will never be disposed
[console.warn] cleanups created outside a `createRoot` or `render` will never be run
```

These warnings are confusing in a React application where developers have no reason to expect Solid.js-related messages. Solid.js is an internal implementation detail of the TanStack Devtools UI layer.

## Steps to Reproduce

```bash
bun install
bun --bun run dev
```

Open <http://localhost:3000> in a browser and check the browser console.

## Expected Behavior

No Solid.js warnings should appear in the browser console of a React application.

## Actual Behavior

Multiple Solid.js warnings appear on page load because the devtools packages pull in different versions of `solid-js` (e.g., `^1.9.9` vs `^1.9.12`), and Solid reactive code initializes at module scope without proper context isolation.

## Environment

- TanStack Start (React, SPA mode)
- Bun
- Vite
- `@tanstack/devtools-vite`
- `@tanstack/react-devtools`
- `@tanstack/react-form-devtools`
- `@tanstack/react-pacer-devtools`
- `@tanstack/react-hotkeys-devtools`
