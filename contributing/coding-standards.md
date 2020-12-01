---
layout: wiki
title: Coding Standards
---
## Coding Standards

 - [Introduction](#introduction)
 - [PHPDoc Blocks](#phpdoc-blocks)
 - [getMockBuilder](#getmockbuilder)
 - [empty()](#empty)
 - [PHP_CodeSniffer](#php_codesniffer)

## Introduction

Generally project is compliment to <a href="https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md" target="_blank">PSR-2</a> coding standards. However additional restrictions are applied:

## PHPDoc Blocks

 - PHPDoc blocks MUST NOT contain any comments. If method requires explanations think about better naming or refactoring.
 - If method has no unsigned or `array` typed parameters and not returning untyped or `array` typed value it MUST NOT have a PHPDoc block.
 - PHPDoc block MUST NOT operate with `array` type. If method expects an array as an argument or returns an array PHPDoc block MUST have the type of array elements (e.g. `string[]`, `MyClass[]`, etc.) defined. This will not only give developers information about your method but will also allow IDE to assign type to variables during `foreach` loop iterations, etc. If it is not possible to explicitly specify type of array elements `mixed[]` annotation MUST be used.
 - Return types `mixed`, `void` or type of array elements (e.g. `string[]`, `MyClass[]`, etc.) MUST be specified in PHPDoc block of interface or abstract methods.

## getMockBuilder

Mocking dependencies during unit testing quite often requires disabling original class constructor. While using verbose mode (e.g. MockBuilder with `disableOriginalConstructor` method) is quite descriptive it is significantly cumbersome increasing visual noise making test less readable. That is one of the most important aspects of a good test. Thus `createMock` MUST be used instead. In rest of cases (e.g. listing mocked methods) MockBuilder MUST be used.

## empty()

PHP `empty()` function is disallowed as risky and error prone. A direct comparison MUST be used instead.

## PHP_CodeSniffer

Every repository Lizards & Pumpkins list [Lizards & Pumpkins Coding Standards](https://github.com/lizards-and-pumpkins/coding-standards) as dev requirement in `composer.json`. Which means once you run `composer install` or `composer update` the latest Lizards & Pumpkins coding standards will be downloaded into `vendor/` directory. You don't need to install PHP_CodeSniffer. The required version will be also downloaded to `vendor/`.

Additionally `composer.json` declares "sniff" command so executing `composer sniff` from the root directory of your project will check your code against Lizards & Pumpkins coding standards.

> Note: Lizards & Pumpkins coding standards are extending PSR-2 standards so you don't need to additionally verify your code against PSR-2 standards.

Optionally you can always run `phpcs` manually specifying path to Lizards & Pumpkins coding standards (e.g. `--standard=./vendor/lizards-and-pumpkins/coding-standards/src/LizardsAndPumpkins`). You can also add path to Lizards & Pumpkins coding standards to installed paths of PHP_CodeSniffer once using following command:

```
./vendor/bin/phpcs --config-set installed_paths ../../lizards-and-pumpkins/coding-standards/src
./vendor/bin/phpcs --config-set show_progress 1
./vendor/bin/phpcs --config-set default_standard LizardsAndPumpkins
```

After this you can run `phpcs` and all default settings are applied (show progress, use "LizardsAndPumpkins" rule set)

> Note: Even though you may already have `phpcs` installed in your system due to issues with different versions we encourage you to use a PHP_CodeSniffer executive located at `./vendor/bin/phpcs`.

If you are using PHPStorm as an IDE you can also include validation against Lizards & Pumpkins coding standards as a part of PHPStorm inspections. Please refer to [this manual](https://www.jetbrains.com/phpstorm/help/using-php-code-sniffer-tool.html) for instructions of configuring PHP_CodeSniffer with PHPStorm.

Additionally you can add PHP_CodeSniffer check to as a Git pre-commit hook. In order to do so add `phpcs` call to your `.git/hooks/pre-commit` file.
