# filament/upgrade crashes with Rector >= 2.8.0

Minimal reproduction: `vendor/bin/filament-v4` fails with

```
Class "Rector\Naming\Guard\BreakingVariableRenameGuard" does not exist
```

`filament/upgrade` allows `rector/rector: ^2.0`, but `Filament\Upgrade\Rector\RenameSchemaParamToMatchTypeRector`
type-hints `Rector\Naming\Guard\BreakingVariableRenameGuard`, which was removed in Rector 2.8.0.

Installed versions (see `composer.lock`): Laravel 12.71.0, Filament 3.3.57, Livewire 3.8.11,
`filament/upgrade` 4.16.1, `rector/rector` 2.8.4, PHP 8.4.

## Steps to reproduce

```bash
composer install
cp .env.example .env && php artisan key:generate
vendor/bin/filament-v4     # press Enter at the directories prompt
```

Result: `Fatal error: Uncaught ReflectionException: Class "Rector\Naming\Guard\BreakingVariableRenameGuard" does not exist`

`app/Filament/Resources/Foo.php` is the only file with Filament v3 code (`Filament\Forms\Form`).

## Workaround (confirms the cause)

```bash
composer require --dev rector/rector:2.7.0 -W
vendor/bin/filament-v4     # completes: "1 file has been changed by Rector"
```
