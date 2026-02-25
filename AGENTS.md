# AGENTS.md

## Project Overview

Calculate Anything is a PHP-based Alfred workflow (macOS) for natural-language calculations: currency, cryptocurrency, units, data storage, percentages, px/em/rem/pt, time, VAT, and color conversions. See `README.md` for full feature documentation.

## Cursor Cloud specific instructions

### Runtime Requirements

- **PHP 8.3** with extensions: `cli`, `xml`, `mbstring`, `curl`, `intl`, `zip`
- **Composer** for dependency management (`jenssegers/date`)
- **phpcs** (PHP_CodeSniffer) and **phpmd** (PHP Mess Detector) for linting — installed globally via Composer

### Missing files restored for development

Two sets of files were deleted from git history but are still referenced by the code:

1. `workflow/lib/backcompat.php` — required by `process.php` but function inside (`money_formatter`) is no longer used. An empty PHP stub is sufficient.
2. `workflow/lib/units/Convertor.php` and `workflow/lib/units/Exceptions/ConvertorInvalidUnitException.php` — the Olifolkerd Convertor library used for unit conversions. These were removed from git tracking but are loaded by `autoload.php`. They must be restored from git history (commit before `d5bceb3`).

### Running the application

This is an Alfred workflow, not a web app. On Linux, invoke `process.php` directly with PHP variables `$query` and `$action`:

```bash
# Unit conversion
php -r '$query = "100km in meters"; $action = ""; include "process.php";'

# Percentage
php -r '$query = "100 + 16%"; $action = ""; include "process.php";'

# CSS units
php -r '$query = "12px"; $action = ""; include "process.php";'

# Time (requires action="time")
php -r '$query = "1577836800"; $action = "time"; include "process.php";'

# VAT (requires action="vat")
php -r '$query = "400"; $action = "vat"; include "process.php";'
```

Output is JSON matching Alfred's Script Filter format. A harmless `Undefined variable $translations` warning appears because Alfred environment variables are not set; this does not affect functionality.

### Lint commands

```bash
export PATH="$HOME/.config/composer/vendor/bin:$PATH"

# PHP_CodeSniffer (exclude vendor/)
phpcs --standard=phpcs.xml --ignore=vendor .

# PHP Mess Detector
phpmd workflow,alfred,process.php text phpmd.xml
```

Both tools report pre-existing code quality issues; there are no automated test suites in this repository.

### Notes

- Currency and cryptocurrency conversions require API keys (`fixer_apikey`, `coinmarket_apikey`) configured as environment variables. Without them, only exchangerate.host (32 currencies, no key needed) works for currency.
- No `composer.lock` is committed (it's in `.gitignore`), so `composer install` resolves dependencies fresh each time.
