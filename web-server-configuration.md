---
layout: wiki
title: Web Server Configuration
---
## Web Server Configuration

## Apache

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

## Nginx

```
location /path/to/pub {
  try_files $uri $uri/ /path/to/pub/index.php?$args;
}

```

## Fallback with Nginx
The example below uses Magento for fallback but can be in fact also Shopware, osCommerce or any other underlying framework.
```
server {
  ...

  location / {
    ...
    try_files $uri @lizards_and_pumpkins;
  }

  location @lizards_and_pumpkins {
    ...    
    fastcgi_param SCRIPT_FILENAME /path/to/pub/index-lizards-and-pumpkins.php;

    fastcgi_intercept_errors on;
    recursive_error_pages on;
    error_page 404 = @magento;
  }

  location @magento {
    ...
    fastcgi_param SCRIPT_FILENAME /path/to/pub/index-magento.php;
  }
}
```

