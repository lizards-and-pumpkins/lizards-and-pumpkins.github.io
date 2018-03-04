---
layout: wiki
title: Configuration
---
## Configuration

- [Web Server](#web-server)
 - [Apache](#apache)
 - [Nginx](#nginx)
- [Environment](#environment)
 - [Command Line](#command-line)
 - [Web Server](#web-server)
- [Configuration Keys](#configuration-keys)
 - [Base URL](#base-url)
 - [File Storage Base Path](#file-storage-base-path)
 - [Host to Website Map](#host-to-website-map)
 - [Catalog Import Directory Path](#catalog-import-directory-path)

## Web Server
### Apache

```
<IfModule mod_rewrite.c>
    Options +FollowSymLinks
    RewriteEngine on
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-l
    RewriteRule .* index.php [L]
</IfModule>
```

### Nginx

```
location /path/to/pub {
  try_files $uri $uri/ /path/to/pub/index.php?$args;
}

```

## Environmnt

Lizards & Pumpkins is configured using the environment only according to [12factor app](http://12factor.net/config) methodology.  
Should an implementation prefer to use a file based configuration, implementing a custom `ConfigReader` should be straight forward.

The default `ConfigReader` implementation is `\LizardsAndPumpkins\EnvironmentConfigReader`.
It is very simple:

It upper-cases any given config key and prefixes it with `LP_`.
Then it attempts to find it in the `$_SERVER` environment variable. If it isn't set, `ConfigReader::get()` will return `null`.  
This means that enforcing the presence of required configuration values is the client responsibility.  

When reading the configuration values the `LP_` prefix is omitted.  
```php
$basePath = $configReader->get('file_storage_base_path');
```

Currently the only required configuration settings are the base URL for each website and the host name to website code mapping.  

The environment has to be configured regardless if the execution environment is the webserver or the command line.

### Command Line

To set up the environment for command line scripts there are several options:  

* Exported variable  
```bash
% export LP_FILE_STORAGE_BASE_PATH="/var/www/htdocs/storage"
```
  or
```bash
% LP_FILE_STORAGE_BASE_PATH="/var/www/htdocs/storage"
% export FILE_STORAGE_BASE_PATH
```
  The variable will then be set for any command run.
* Command only  
```bash
% LP_FILE_STORAGE_BASE_PATH="/var/www/htdocs/storage" LP_BASE_URL_DEFAULT="http://lizards-and-pumpkins.dev/" bin/runImport.php -c import/catalog.xml
```
  The environment variables will only be set for the invoked command.

* Command Arguments  
  Most Lizards&Pumpkins PHP commands also allow specifying environment settings using the `-e` argument.  
```bash
% bin/runImport.php -c import/catalog.xml -e file_storage_base_path=./storage
```

### Webserver

For Apache with mod_php or php-fpm the environment can be set using the [`SetEnv`](https://httpd.apache.org/docs/2.4/env.html) and related configuration directives.  

Example (Apache):  
```
<VirtualHost *:80>
    ServerName lizards-and-pumpkins.dev
    DocumentRoot "/var/www/htdocs/pub"
    DirectoryIndex index.php
    SetEnv LP_FILE_STORAGE_BASE_PATH /var/www/htdocs/storage
    SetEnv LP_BASE_URL_EXAMPLE http://lizards-and-pumpkins.dev/
    SetEnv LP_BASE_URL_TO_WEBSITE_MAP lizards-and-pumpkins.dev=example|www.lizpump.dev=example
</VirtualHost>
```

Example (NGINX):
```
server {
  listen 80;
  server_name lizards-and-pumpkins.dev;
  root /var/www/htdocs/pub;
  index index.php;

  location ~ \.php {
    fastcgi_param LP_FILE_STORAGE_BASE_PATH "/var/www/htdocs/storage";
    fastcgi_param LP_BASE_URL_EXAMPLE "http://lizards-and-pumpkins.dev/";
    fastcgi_param LP_BASE_URL_TO_WEBSITE_MAP "lizards-and-pumpkins.dev=example|www.lizpump.dev=example";
  }
}
```

## Configuration Keys

### Base URL

**Key:** `LP_BASE_URL_<WEBSITE_CODE>`   
The base URL for every website is required to be part of the configuration.
The website code is taken from the context, more specifically the `WebsiteContextDecorator`.  
A valid base URL has to start with `http://` or `https://` and end with a trailing `/`.

### File Storage Base Path

**Key:** `LP_FILE_STORAGE_BASE_PATH`  
This configuration setting is only used if a file based implementation for the queue, key-value store or search engine is used.  
The value should be the absolute path to the storage base directory, without a trailing slash.

### Host to Website Map

**Key:** `LP_BASE_URL_TO_WEBSITE_MAP`  
This configuration setting is used to know the website for a given request. It consists of host name and website code pairs, separated with an `=` character.  
Example: `lizards-and-pumpkins.dev=example`  
Here the host is the domain name `lizards-and-pumpkins.dev` and the website code is `example`. Of course the host could also be an IP address (only version 4 IP addresses are supported currently).  
Multiple mapping pairs are separated with a `|` character.  
Example: `lizards-and-pumpkins.dev=example|www.lizards-and-pumpkins.dev=example|example.com=reseller`  

### Catalog Import Directory Path

**Kry:** `LP_CATALOG_IMPORT_DIRECTORY`
Defines the path to a directory where catalog import files and product images are stored.
