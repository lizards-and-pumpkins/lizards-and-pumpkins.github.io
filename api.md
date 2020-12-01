---
layout: wiki
title: API
---
## API

- [Introduction](#introduction)
- [Authentication](#authentication)
- [Catalog Management API](#catalog-management-api)
- [Public Catalog Query API](#public-catalog-query-api)
- [Request Flow](#request-flow)

## Introduction

Lizards & Pumpkins is based on API-first architecture. This means there is no tight coupling between content and presentation layer. Whatever you want your shop to be Angular-like website, native app, classical website or all above (!) Lizards & Pumpkins allows you to do that.

Tradition method of designing software prescribes to build end-to-end application and then optionally expose an API for possible variations. API-first approach suggest defining an API first and then building a presentation layer on top of it.

## Authentication

There are no authentication implementations yet. We suggest protecting sensitive endpoints on infrastructural level.

## Catalog Management API

The catalog management API is documented in form of a swagger OpenAPI specification.
https://github.com/lizards-and-pumpkins/catalog/blob/master/swagger/management-api.yaml

## Public Catalog Query API

The public catalog API is not yet documented with swagger (coming soon).
It currently consists of two endpoints: the product search API and the product relations API.

### Product Search API

There is currently no documentation available. A swagger specification is being worked on.
Please refer to https://github.com/lizards-and-pumpkins/catalog/issues/1037 for more information.

### Product Relations API


```
GET /products/:id/relations/related-models
```

The product relations API is implemented as an extension point, but the concrete types of product relations are specific to each project or to third party personalized recommendation service providers.
Any relations will be available below the URL path `/products/:id/relations/:relation_type`.
For example: `GET /products/mg123/relations/related-models`

## Request Flow

![](http://www.websequencediagrams.com/cgi-bin/cdraw?lz=dGl0bGUgUkVTVCBBUEkgV29ya2Zsb3cKCldlYkZyb250LT4rSHR0cFJvdXRlckNoYWluOiByb3V0ZSgAEgVlcXVlc3QpCgAVDwApDQASHy0-K0FwaQAQGwAVCQAjBwBuBkhhbmRsAIEKCWdldAAKEShjb2RlOnN0cmluZwBABgAoEi0-LQBzCgCBRwsAVwcAcAwtAIFGDAAZEiB8IG51bGwAgUwNACgLAII0BwBQEwCCKhEtAIJ0CAAaFQCDCRAAggENOiBwcm9jZXNzKACDDAcAgh8NAEgTc3BvbnNl&s=default)
