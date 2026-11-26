# Thyreon

<div align="center">

![Thyreon](public/images/home-hero--480x480.png)

_WordPress Maintenance Made Easy_

[![Live Demo](https://img.shields.io/badge/demo-live-brightgreen)](https://thyreon.jwd-apps.com)
![Next.js 16](https://img.shields.io/badge/Next.js-16-black)
![React 19](https://img.shields.io/badge/React-19-61DAFB)
![TypeScript 5.9](https://img.shields.io/badge/TypeScript-5.9-3178C6)
![MUI 7](https://img.shields.io/badge/MUI-7-007FFF)
[![Secret scan](https://github.com/fcidevel/Thyreon-FrontEnd/actions/workflows/gitleaks.yml/badge.svg?branch=main)](https://github.com/fcidevel/Thyreon-FrontEnd/actions/workflows/gitleaks.yml)

</div>

---

## Overview

**Thyreon** is a Next.js-based web application that provides a centralized dashboard for WordPress site management. Built for freelancers, agencies, and site owners, it offers an intuitive interface to monitor, maintain, and secure multiple WordPress websites.

---

This is the front-end application for the Thyreon platform, providing:

- **User Interface** for managing multiple WordPress sites
- **Dashboard** with chart widgets for monitoring and analytics
- **Authentication** via Clerk (Google, GitHub and Facebook sign-in, or email and password)
- **Role-Based Access Control** (Admin, Premium, Pro, Lite)
- **Subscription Management** with Stripe integration
- **GraphQL Integration** with the Thyreon back-end API

---

## Tech Stack

| Category            | Technology                                                                    |
| ------------------- | ----------------------------------------------------------------------------- |
| **Framework**       | Next.js 16 (App Router)                                                       |
| **Language**        | TypeScript 5.9                                                                |
| **Runtime**         | React 19                                                                      |
| **Authentication**  | Clerk (JWT sessions, RBAC)                                                    |
| **GraphQL Client**  | Apollo Client 4                                                               |
| **UI Framework**    | Material UI (MUI) 7                                                           |
| **Data Tables**     | MUI X Data Grid 8                                                             |
| **Charts**          | MUI X Charts 8                                                                |
| **Styling**         | Tailwind CSS 4 + Emotion + Styled Components                                  |
| **Icons**           | Bootstrap Icons                                                               |
| **State**           | Zustand 5 + Apollo Cache                                                      |
| **Real-time**       | Socket.IO Client                                                              |
| **Payments**        | Stripe.js                                                                     |
| **Hosting**         | [Vercel](https://vercel.com)                                                  |
| **Linting**         | ESLint 9 + typescript-eslint                                                  |
| **Dead Code**       | Knip                                                                          |
| **Secret scanning** | [gitleaks](https://github.com/gitleaks/gitleaks) - see [Secret scanning](#secret-scanning-mandatory) |

---

## Features

### User-Facing

- **Landing Page** -- Public homepage with hero, features, how-it-works, pricing, and FAQ sections
- **Authentication** -- Secure sign-in via Clerk (Google, GitHub and Facebook, or email and password)
- **User Dashboard** -- Personal dashboard with chart widgets (bar, line, pie, scatter and sparkline; demo data)
- **Site Management** -- Add, edit, delete sites; view site details across parallel tab routes (info, health, reports, backups, security, updates, settings)
- **Account Page** -- Account hero, billing info, and linked sites
- **Subscription Plans** -- Plan comparison cards with Stripe checkout integration
- **Notifications** -- User notification feed (demo data)
- **Settings** -- Theme toggle (light/dark)
- **FAQs** -- Accordion-style FAQ section (shared between landing and app)
- **Responsive Design** -- Mobile-first, fully responsive UI

### Admin Panel

- User management (view, create, edit, quick-edit, delete) with data table
- Site management and monitoring
- Transaction history with data table
- API key regeneration per site
- Charts and visualizations (bar, line, pie, half-pie, scatter, sparkline via MUI X Charts)

### Technical

- **Server-Side Rendering** -- Next.js App Router with server components and server actions
- **GraphQL API Layer** -- Apollo Client 4 with auth-linked queries, cache-and-network fetch policy, and query deduplication
- **Real-Time Updates** -- Socket.IO listeners for user created / updated / deleted events
- **Theme Support** -- Light and dark mode via MUI `InitColorSchemeScript` with custom palettes
- **State Management** -- Zustand stores for dialog and page-level state; Apollo in-memory cache for server data
- **Role-Based Access** -- `AdminAuthContext` provides role flags (`isAdmin`, `isPremium`, `isPro`, `isLite`) across the component tree
- **Type Safety** -- Strict TypeScript config with `noUnusedLocals` and `noUnusedParameters` enabled

---

## Project Structure

```
.
├── .githooks/
│   ├── pre-commit           # gitleaks scan of the staged changes
│   └── pre-push             # gitleaks scan of every commit being pushed
├── .github/workflows/
│   └── gitleaks.yml         # "Secret scan" CI job
├── public/images/           # landing-page and brand images
├── src/
│   ├── app/                 # App Router: (public), (app) and (admin) route groups, parallel routes
│   ├── components/          # layout, page sections and shared components
│   ├── constants/           # GraphQL documents, navigation, table columns, form fields, demo data
│   ├── context/             # admin auth (role flags) and admin UI contexts
│   ├── lib/                 # server actions, Apollo client, hooks, Zustand stores, utilities
│   ├── styles/              # CSS modules
│   ├── themes/              # MUI theme and light/dark palettes
│   ├── types/               # shared TypeScript types
│   └── proxy.tsx            # route protection (Clerk)
├── .gitattributes
├── .gitignore
├── .gitleaks.toml           # gitleaks rules: built-in + project-specific
├── build-check.sh           # Vercel ignored-build-step script (builds main only)
├── css-modules.d.ts
├── eslint.config.mjs
├── knip.json
├── LICENSE
├── next.config.ts
├── package.json
├── package-lock.json
├── postcss.config.mjs
└── tsconfig.json
```

---

## Secret scanning (mandatory)

Every change is checked for leaked credentials with
[gitleaks](https://github.com/gitleaks/gitleaks), at three layers:

| Layer  | What runs                                                                                                    | What it blocks                          |
| ------ | ------------------------------------------------------------------------------------------------------------ | --------------------------------------- |
| Commit | `.githooks/pre-commit` - scans the staged changes                                                            | the commit                              |
| Push   | `.githooks/pre-push` - scans every commit being pushed                                                       | the push                                |
| GitHub | `Secret scan` job (`.github/workflows/gitleaks.yml`) - scans the full history on every push and pull request | updating `main` (required status check) |

- **No credentials in the repository.** Real values are never committed; the app reads
  them from its deployment environment.
- **Project-specific rules.** [`.gitleaks.toml`](.gitleaks.toml) keeps every built-in
  gitleaks rule and adds two: webhook signing secrets (Clerk and Stripe) and Context7
  API keys, which development tooling uses.
- **Hooks on by default.** Installing the dependencies switches the hooks on for that
  clone.
- **Supply-chain hardening.** The CI job pins `actions/checkout` to a commit SHA and
  verifies the gitleaks release binary against its published checksum.
- **Leaked-secret policy.** A secret that ever reaches GitHub is treated as
  compromised and rotated at the source immediately.

---

## Security

### Application

- **Route protection** - every route except the landing, sign-in, sign-up and error
  pages redirects signed-out users to sign-in, and `/admin` redirects users without the
  `admin` role claim to `/401`, in [`src/proxy.tsx`](src/proxy.tsx).
- **Authenticated API calls** - requests to the Thyreon back-end carry the user's Clerk
  session token as a Bearer token, from both the Apollo client and the server actions.

### Runtime

The app is pinned to the **Node.js 24.x** LTS line through the `engines` field in
[`package.json`](package.json), matching the hosting setting. Development uses
**v24.22.3** or later, which includes the
[November 2026 Node.js security release](https://nodejs.org/en/blog/vulnerability/november-2026-security-releases).

### Repository protection

Only the repository owner can change this repository. Two GitHub rulesets enforce it:

| Ruleset          | Applies to              | Rules                                                         | Bypass                          |
| ---------------- | ----------------------- | ------------------------------------------------------------- | ------------------------------- |
| Owner Only Rule  | all branches            | restrict creations, updates and deletions; block force pushes | repository admin (the owner)    |
| Secret Scan Rule | default branch (`main`) | `Secret scan` must pass; block deletions and force pushes     | none - applies to the owner too |

A commit can reach `main` only after `Secret scan` has passed on it, and nobody can
force-push or delete `main`.

---

## Author

Built with ❤️ by [FCI Devel](https://fcidevel.com)

---

## License

Source-available, not open source. The code is public so that prospective clients,
partners and employers can review how FCI Devel builds software: you may read it, and
clone and run it on your own devices for that review, but you may not reuse it.
Contributions are not accepted, and security issues are reported privately as the
licence describes.

See [LICENSE](LICENSE) for the full terms.
