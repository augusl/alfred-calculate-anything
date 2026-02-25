# AGENTS.md

## Cursor Cloud specific instructions

This is **Calculate Anything**, an Alfred 5 workflow (macOS plugin) written in PHP that performs natural language calculations and conversions (currency, cryptocurrency, units, percentages, px/em/rem, time, VAT, data storage, color).

### Project structure

- `process.php` — main entry point invoked by Alfred; expects `$query` and `$action` PHP variables
- `alfred/Alfred.php` — helper functions for Alfred integration (env vars, translations, file I/O)
- `workflow/calculateanything.php` — core `CalculateAnything` class
- `workflow/tools/` — individual calculator modules (currency, units, percentage, etc.)
- `workflow/lib/` — bundled third-party libs (e.g. Convertor for units)
- `lang/` — translation files (`en_EN.php`, `es_ES.php`, `sv_SE.php` + `-keys.php` variants)
- `data/` — static data files (e.g. `crypto-currencies.json`)

### Running the application

Since this is an Alfred workflow (not a web app), test via CLI:

```bash
php -r '$query = "100km to m"; $action = ""; include "process.php";'
```

For keyword-triggered actions (time, vat, color), set `$action`:

```bash
php -r '$query = "+15 days"; $action = "time"; include "process.php";'
php -r '$query = "of 400"; $action = "vat"; putenv("vat_percentage=16"); include "process.php";'
```

Output is Alfred-compatible JSON (`{"items": [...]}`).

### Linting

```bash
phpcs --standard=phpcs.xml workflow/ alfred/ process.php
phpmd workflow/ text phpmd.xml
```

`phpcs` and `phpmd` are installed globally via Composer (`~/.config/composer/vendor/bin/`). Ensure `$HOME/.config/composer/vendor/bin` is in `PATH`.

### Dependencies

- Runtime: `composer install` (installs `jenssegers/date`)
- Dev tools: `phpcs` and `phpmd` (installed globally via `composer global require`)
- No automated test suite exists in the repository; validation is done via CLI execution
- Currency/crypto features require external API keys (fixer.io, coinmarketcap.com) — these are optional for core unit/percentage/time/vat/px testing
