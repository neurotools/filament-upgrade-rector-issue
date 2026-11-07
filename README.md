# Reproduction: `filament/upgrade` crashes with Rector >= 2.8.0

Built from the [Filament issue reproduction template](https://github.com/filamentphp/issue-reproduction-template) (`3.x` branch: Laravel 12 + Filament v3), plus `filament/upgrade` ^4.0 as a dev dependency.

`vendor/bin/filament-v4` fails with:

```
Class "Rector\Naming\Guard\BreakingVariableRenameGuard" does not exist
```

`filament/upgrade` allows `rector/rector: ^2.0`, but `Filament\Upgrade\Rector\RenameSchemaParamToMatchTypeRector` type-hints `Rector\Naming\Guard\BreakingVariableRenameGuard`, which was removed in Rector 2.8.0.

Versions (see `composer.lock`): Laravel 12.71.0, Filament 3.3.57, Livewire 3.8.11, `filament/upgrade` 4.16.1, `rector/rector` 2.8.4.

## Steps to reproduce

```bash
git clone https://github.com/neurotools/filament-upgrade-rector-issue.git
cd filament-upgrade-rector-issue
composer setup
vendor/bin/filament-v4     # press Enter at the directories prompt
```

Result: `Fatal error: Uncaught ReflectionException: Class "Rector\Naming\Guard\BreakingVariableRenameGuard" does not exist`

`app/Filament/Resources/Foo.php` is the only file added to the template. It contains Filament v3 code (`Filament\Forms\Form`) so there is something to process.

## Workaround (confirms the cause)

```bash
composer require --dev rector/rector:2.7.0 -W
vendor/bin/filament-v4     # completes: "files have been changed by Rector"
```

## Changes compared to the template's `3.x` branch

- Added `filament/upgrade` ^4.0 (`require-dev`) and `composer.lock`.
- Added `app/Filament/Resources/Foo.php`.
- `app/Filament/Pages/Auth/Login.php`: the `3.x` branch imports `Filament\Auth\Pages\Login` (a v4 class), which makes the app fail to boot on Filament v3. Changed it to `Filament\Pages\Auth\Login`.
