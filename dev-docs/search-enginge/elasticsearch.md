---
layout: wiki
title: Elasticsearch
---
## Elasticsearch

> This page is a draft

Here is a mapping used for integration tests:


> curl -XPUT 'localhost:9200/your-index-name' -H 'Content-type: application/json' -d '{"mappings": {"your-index-name": {"properties": {"version": {"type": "keyword", "include_in_all": false}, "locale": {"type": "keyword", "include_in_all": false}, "gender": {"type": "keyword"}, "brand": {"type": "keyword"}, "style": {"type": "keyword"}, "product_id": {"type": "keyword", "include_in_all": false}, "size": {"type": "keyword"}, "color": {"type": "keyword"}, "product_group": {"type": "keyword"}, "series": {"type": "keyword"}, "price_incl_tax_en": {"type": "integer", "include_in_all": false}, "category": {"type": "text"}, "foo": {"type": "keyword"}, "id": {"type": "keyword"}, "version": {"type": "keyword", "include_in_all": false}, "website": {"type": "keyword", "include_in_all": false}, "bar": {"type": "keyword"}, "price": {"type": "integer", "include_in_all": false}}}}}'
