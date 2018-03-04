---
layout: wiki
title: Getting Started
---
## Getting Started

This guide assumes you already have a running shop and would like to use Lizards & Pumpkins as a front-end. If you yet don't have anything and just want to play with the system you might be interested in [Demo VM](/dev-docs/demo-vm/) or [Docker DevBox](/dev-docs/docker-devbox/).

### How it works

Lizards & Pumpkins conceptually contains of three parts: exporting data from your leading system (Shop, PIM, etc), importing it into Lizards & Pumpkins and delivery of a content to an end-user.

Export can be accomplished via one of the [connectors](/dev-docs/connectors/). If none is available for your system you probably have to build one yourself.

Exported data is then sent to Lizards & Pumpkins via [API](/dev-docs/api/) for import. Anytime stocks, prices or any other product data is changed the leading system is supposed to send an update to Lizards & Pumpkins.

### Installation

The most straightforward way to install Lizards & Pumpkins is via <a href="https://getcomposer.org/" target="_blank">composer</a>. Add following directives to your `composer.json` file:

```
{
  "require": {
    "lizards-and-pumpkins/catalog": "~3.1.0",
    "lizards-and-pumpkins/lib-key-value-store-filesystem": "~1.0.0",
    "lizards-and-pumpkins/lib-search-engine-filesystem": "~1.0.0",
    "lizards-and-pumpkins/lib-queue-filesystem": "~1.0.0",
    "lizards-and-pumpkins/lib-image-processing-imagick": "~1.0.0"
  },
  "autoload": {
    "psr-4": {"LizardsAndPumpkins\\": "src/lizards-and-pumpkins/src"}
  }
}
```
and run `composer update`. The `src/lizards-and-pumpkins/src` could be anything else. This is the path where you would keep you implementation specific Lizards & Pumpkins files. And don't worry about "filesystem" based search engine and key-value store. You will be able to replace them with technologies of your choice later.

### Boilerplate

The minimum code required for Lizards & Pumpkins to run is a `Util/Factory/ProjectFactory.php` file, however for a quicker start you may want to use our <a href="https://github.com/lizards-and-pumpkins/sample-project" target="_blank">boilerplate project</a>. Copy `src/` and `theme/` directories from `src/lizards-and-pumpkins/` to your project. Remember the arbitrary path in the previous section? Yes, it is it. There are lots of things which could be adjusted there but for now we can use it as is.

### Configuration

Lizards & Pumpkins stores its [configuration](/dev-docs/configuration/#environmnt) in the environment. Let's say you have English, German and French websites. In this case minimal configuration would be: 

```
export LP_BASE_URL_EN="http://example.loc/en/"
export LP_BASE_URL_DE="http://example.loc/de/"
export LP_BASE_URL_FR="http://example.loc/fr/"
export LP_BASE_URL_TO_WEBSITE_MAP="http://example.loc/=en|http://example.loc/en/=en|http://example.loc/de/=de|http://example.loc/fr/=fr"
export LP_MEDIA_BASE_PATH="/var/www/shop/pub/media"
export LP_FILE_STORAGE_BASE_PATH="/var/www/shop/file-storage"
export LP_LOG_FILE_PATH="/var/www/shop/system.log"
```

After executing this you will be able to use Lizards & Pumpkins [CLI tool](/dev-docs/cli-tool/). For example running:

```
vendor/bin/lp data-version:get
```

will return:

```
Current data version:  -1
Previous data version: 
```

Got the same output? Splendid! Let's move on!

### Import

If you are using one of the [connectors](/dev-docs/connectors/) you may want to run it at this point. The connector will aggregate data in your leading system and then will trigger a Lizards & Pumpkins [REST API](/dev-docs/api/) endpoint asking to import it. In this case you don't have much to do here. Some configuration must be done at the connector side of course. Please refer to you connector manual.

Import can also be triggered manually via CLI. Let's say you already have an XML file containing all or some products of your catalog. So for example here is how manual import with a sample fixture would look like:

```
vendor/bin/lp import:catalog -p vendor/lizards-and-pumpkins/catalog/tests/shared-fixture/catalog.xml
```

The `-p` flag tells Lizards & Pumpkins to process import file right away as we yet have no [workers](/dev-docs/events-commands-workers/) running.

Finished? No errors at stdout? Anything imported? Let's check!

```
vendor/bin/lp report:url-keys
```

This will output all URL keys available in the system. If there's nothing you may want to `cat $LP_LOG_FILE_PATH` and check for errors there. Got some URL keys? Yikes! You are mastering Lizards & Pumpkins.

### Front Controllers

Let's say your shop already have a font controller, something like `pub/index.php`. Let's add more of them! If you are planning to use Lizards & Pumpkins [content delivery](/dev-docs/content-delivery/) to serve catalog pages you will need to add a new front controller. Let's create a `pub/index-lizards-and-pumpkins.php` with following contents:

```
<?php

namespace LizardsAndPumpkins;

use LizardsAndPumpkins\Http\HttpRequest;
use LizardsAndPumpkins\Util\Factory\ProjectFactory;

require_once '../vendor/autoload.php';

$request = HttpRequest::fromGlobalState(file_get_contents('php://input'));
$implementationSpecificFactory = new ProjectFactory();

(new DefaultWebFront($request, $implementationSpecificFactory))->run();
```

Note that depending on your directory structure you may need to adjust `vendor/autoload.php` path.

If you are planning to retrieve data from Lizards & Pumpkins [REST API](/dev-docs/api/) you will need to add a correspondent controller. Let's name it `pub/lizards-and-pumpkins-rest-api.php` and put the following content in:

```
<?php

namespace LizardsAndPumpkins;

use LizardsAndPumpkins\Http\HttpRequest;
use LizardsAndPumpkins\Util\Factory\ProjectFactory;

require_once '../vendor/autoload.php';

$request = HttpRequest::fromGlobalState(file_get_contents('php://input'));
$implementationSpecificFactory = new ProjectFactory();

(new RestApiWebFront($request, $implementationSpecificFactory))->run();
```

Same here, depending on your directory structure `vendor/autoload.php` path may be adjusted.

There is one single step left before Lizards & Pumpkins will be able to serve web requests.

### Web Server

Regardless of will you use Lizards & Pumpkins [content delivery](/dev-docs/content-delivery/) or only [REST API](/dev-docs/api/) to feed your application you need a web server. 

First we need to add same variables for web server environment as we did for CLI. For Apache the following lines must be added to virtual host configuration:

```
SetEnv LP_BASE_URL_FR "http://example.loc/fr"
SetEnv LP_BASE_URL_DE "http://example.loc/de"
SetEnv LP_BASE_URL_EN "http://example.loc/en"
SetEnv LP_BASE_URL_TO_WEBSITE_MAP "http://example.loc/=en|http://example.loc/en/=en|http://example.loc/de/=de|http://example.loc/fr/=fr"
SetEnv LP_FILE_STORAGE_BASE_PATH /var/www/shop/file-storage
SetEnv LP_LOG_FILE_PATH /var/www/shop/system.log
```

same would be for NGINX with the only difference in syntax:

```
fastcgi_param LP_BASE_URL_FR "http://example.loc/fr";
fastcgi_param LP_BASE_URL_DE "http://example.loc/de";
fastcgi_param LP_BASE_URL_EN "http://example.loc/en";
fastcgi_param LP_BASE_URL_TO_WEBSITE_MAP "http://example.loc/=en|http://example.loc/en/=en|http://example.loc/de/=de|http://example.loc/fr/=fr";
fastcgi_param LP_FILE_STORAGE_BASE_PATH "/var/www/shop/file-storage";
fastcgi_param LP_LOG_FILE_PATH "/var/www/shop/system.log";
```

Then the rewriting rules must be added. For Apache the following lines must be added to `/` location. 

```
<IfModule mod_rewrite.c>
    Options +FollowSymLinks
    RewriteEngine on
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-l
    RewriteRule .* index-lizards-and-pumpkins.php [L]
</IfModule>
```

For NGINX it would be much more concise:

```
try_files $uri $uri/ /path/to/pub/index-lizards-and-pumpkins.php?$args;
```

The same must be done for `/api/` location and `lizards-and-pumpkins-rest-api.php` endpoint.

Restart your web server and you are good to perform your fist [REST API](/dev-docs/api/) call.

```
curl -X GET --header "Accept: application/vnd.lizards-and-pumpkins.current_version.v1+json" example.loc/api/current_version
```

must return:

```
{"data":{"current_version":"-1","previous_version":""}}
```

Same request as we performed through CLI back in the configuration section, remember? Now through REST API.

Let's now make something more meaningful.

```
curl -X GET --header "Accept: application/vnd.lizards-and-pumpkins.product.v1+json" example.loc/api/product/?q=adi
```

This would return 2 products matching "adi" query string. That was easy, right? Please refer to [REST API](/dev-docs/api/) page for the full reference.

Now what about web pages? Before those can be delivered we need to import 2 templates. One for product listing and another for a product page itself:

```
vendor/bin/lp import:template -p product_listing
vendor/bin/lp import:template -p product_detail_view
```

Now for example `curl example.loc/sale` will return a listing page HTML with some products. Opening this in a browser will show some broken page as stylesheets, JavaScript files and images are missing. You can either copy them from a <a href="https://github.com/lizards-and-pumpkins/sample-project/tree/master/src/lizards-and-pumpkins/pub" target="_blank">boilerplate project</a> or start adopting the theme to your shop identity.

### Next Steps

Where do I go from here? If you are using Lizards & Pumpkins to feed your ReactJS, Angular or similar application you may want to have a deeper dive into a [REST API](/dev-docs/api/). Otherwise it may be worthy to go into details of [Projection](/dev-docs/projection/) and [Content Delivery](/dev-docs/content-delivery/).

Found this tutorial hard to use? Any steps missing? Please [contact us](/contact/) and we will be happy to improve it.
