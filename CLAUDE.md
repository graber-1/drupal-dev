# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Drupal 11 module development stack using DDEV for local Docker-based environments. Development centers on the `drupal/contrib` modules folder.

## Environment Setup

```bash
ddev start
ddev composer install
ddev composer update drupal/lms
ddev composer si         # Install site with existing config
ddev composer sid        # Install with demo content + simple passwords
```

## Commands

All commands run **inside** the DDEV container. Either prefix with `ddev` or use `ddev ssh` first.

**Site install:**
```bash
ddev composer si                  # Minimal install with existing config
ddev composer sid                 # With demo data and simple passwords
ddev composer test-environment    # Initial state for functional JS tests
```

**Testing (run from inside `ddev ssh`):**
```bash
mkdir web/sites/default/files/simpletest/browser_output  # first time only
ddev phpunit [module_name]          # PHPUnit tests for a module in the web/modules/contrib folder
```
Module must be in `web/modules/contrib/[module_name]`.

**Code quality (inside `ddev ssh`):**
```bash
composer phpcs [module_name]              # Check code style
composer phpcbf [module_name]             # Auto-fix code style
composer phpstan [module_name]            # Static analysis
composer phpstan-baseline [module_name]   # Regenerate PHPStan baseline
```

## Architecture

- `web/` — Drupal document root
- `web/modules/contrib/` — Contributed modules folder
- `config/sync/` — Exported Drupal configuration (YAML, ~83 files); config changes are managed here
- `web/sites/default/settings.ddev.php` — DDEV-injected database/environment settings

### Config Management

Site configuration is managed via Drupal's config sync system. After making config changes in the UI, export with `drush cex` and commit the resulting YAML files in `config/sync/`.

## Code Standards

All custom PHP must use `declare(strict_types=1);` and follow Drupal 11 OOP patterns:

- **Hooks**: Use Drupal 11 OOP hook pattern (not procedural hooks)
- **Services**: Use dependency injection via services.yml
- **No procedural code** in custom modules except `hook_install`/`hook_update_N` where required

PHPStan and PHPCS must pass before considering work complete. Each module maintains its own `phpstan.neon` and `phpcs.xml`.
