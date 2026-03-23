# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Bookkeeper is a fork of [Akaunting](https://akaunting.com) — open-source online accounting software built on **Laravel 10 + Vue 2.7 + Livewire 3 + Tailwind CSS 3**. It uses Laravel Mix (Webpack 5) for asset compilation.

- **Remotes**: `origin` = fork (BlackfishSpirit/akaunting), `upstream` = akaunting/akaunting
- **PHP**: 8.1+ required (CI tests on 8.1–8.3). Local dev uses PHP 8.4 with deprecation warnings suppressed in `php.ini`.
- **Node**: 22 (uses `sass` package, NOT `node-sass`)

## Common Commands

```bash
# Backend
composer install --ignore-platform-req=php   # PHP 8.4 needs platform override
php artisan serve                             # Dev server at http://127.0.0.1:8000
php artisan test                              # Run PHPUnit tests
php artisan test --filter=SomeTest            # Run a single test class/method

# Frontend
npm run dev                                   # Build assets (development)
npm run watch                                 # Watch mode with auto-rebuild
npm run production                            # Production build (minified)

# Database
php artisan migrate                           # Run migrations
php artisan sample-data:seed                  # Seed sample data

# Upstream sync
git fetch upstream && git merge upstream/master
```

## Architecture

### Backend (Laravel MVC)

- **Routes**: 12 route files in `/routes/` — key ones: `admin.php` (authenticated admin), `api.php` (RESTful API with Sanctum), `portal.php` (client portal), `wizard.php` (setup wizard), `guest.php` (public)
- **Controllers**: `/app/Http/Controllers/` organized by domain: `Auth/`, `Banking/`, `Common/`, `Sales/`, `Purchases/`, `Settings/`
- **Models**: `/app/Models/` — same domain structure. Key models: `Document` (invoices/bills), `Transaction`, `Account`, `Contact`, `Company`
- **Auth**: Session-based (web guard) + Sanctum tokens (API guard). RBAC via Laratrust (roles/permissions)
- **Modules**: `/modules/` — pluggable extensions (OfflinePayments, PaypalStandard). Uses Akaunting's custom module system, not Laravel Modules.
- **Service Providers**: `/app/Providers/` — Binding, Observer, Event, Route, Validation, etc.

### Frontend (Vue 2 + Blade + Livewire)

- **Vue entry points**: `/resources/assets/js/views/` — one per domain (banking, common, sales, purchases, settings, wizard, install)
- **Vue components**: `/resources/assets/js/components/` — reusable UI components
- **Blade templates**: `/resources/views/` — layout components in `components/layouts/` (admin, portal, wizard, print)
- **Livewire components**: `/app/Http/Livewire/`
- **Build config**: `webpack.mix.js` compiles Vue SFCs + JS bundles to `/public/js/`

### Database

- **Engine**: MySQL 8.0 (local dev via Docker: `docker run --name akaunting-db -e MYSQL_ROOT_PASSWORD=pass -e MYSQL_DATABASE=akaunting -p 3306:3306 -d mysql:8.0`)
- **Test DB**: SQLite in-memory (configured in `phpunit.xml`)
- **Migrations**: `/database/migrations/`

## Known Issues & Workarounds

Check `.claude/tribal-knowledge.jsonl` for detailed troubleshooting entries. Key issues:

- **Asset double-path**: Blade templates use `asset('public/...')` but `artisan serve` already serves from `public/`. Workaround: symlink `public/public -> public`. Proper fix tracked in `.claude/tasks.json` (task_001).
- **PHP 8.4 deprecations**: Implicit nullable parameters cause warnings. Suppressed via `error_reporting = E_ALL & ~E_DEPRECATED` in `php.ini`. App-level fixes tracked as task_002.
- **Wizard blank page**: Vue error in `Company.vue`. Skip with the skip button or `php artisan tinker --execute="setting(['wizard.completed' => '1']);"`.
- **APP_URL must be set**: Set `APP_URL=http://127.0.0.1:8000` in `.env` before running `artisan serve`.

## Task & Knowledge Tracking

- **Planned tasks**: `.claude/tasks.json` — canonical task list for this project
- **Tribal knowledge**: `.claude/tribal-knowledge.jsonl` — search with `grep -i "keyword"` before debugging
- **Schema**: `.claude/tribal-knowledge-schema.md`

## Multi-Company Architecture

Akaunting is multi-tenant by company. Routes are prefixed with `/{company_id}/`. The `Company` middleware resolves the current company from the URL. Models are scoped to the current company via Eloquent global scopes.
