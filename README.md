# WXT + Vue 3

This template should help get you started developing with Vue 3 in WXT.

## Project Summary

This project is a starter template for building browser extensions using [WXT](https://wxt.dev) and [Vue 3](https://vuejs.org/). It provides a modern development environment with Vue 3 as the frontend framework, TypeScript for type safety, and WXT for browser extension tooling.

## Key Features

- **Vue 3** for building the user interface.
- **WXT** for browser extension tooling and build processes.
- **TypeScript** for type safety.
- **Volar** recommended for enhanced IDE support.

## Project Structure

- `entrypoints/` contains extension entry points:
  - `background.ts` and `content.ts` for background and content scripts.
  - `popup/` for the extension popup UI, built with Vue (`App.vue`, `main.ts`, etc.).
- `components/HelloWorld.vue` is a sample Vue component demonstrating reactivity and hot module replacement (HMR).
- `public/`, `assets/`, and other standard directories for static files and assets.

## Scripts

- `dev`, `build`, `zip` for development, building, and packaging the extension.
- Firefox-specific scripts for cross-browser support.

## Recommended IDE Setup

- [VS Code](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar).
