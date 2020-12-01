---
layout: wiki
title: Filesystem Key Value Store
---
## Filesystem Key Value Store

> Warning: This library is intended to be used at development environments only. For production stores please install Redis or Memcached implementation.

This library is a "technology stub" of `KeyValueStore` interface. It provides slow but very visual key/value store implementation which can be handy during development process.

## Installation

Same as any other library it can be installed either via composer:

```json
"require": {
    "lizards-and-pumpkins/lib-key-value-store-filesystem": "dev-master"
}
```

Or repository can be cloned into a place where auto-loader will be able to pick it up.

## Usage

Filesystem key/value store library must be instantiated in `createKeyValueStore` method of implementation specific factory:

```php
public function createKeyValueStore() : KeyValueStore
{
    $storageBasePath = $this->getMasterFactory()->getFileStorageBasePathConfig();
    $storagePath = $storageBasePath . '/key-value-store';

    // Maybe ensure that storage path exists and create otherwise
    // ...

    return new FileKeyValueStore($storagePath);
}
```
