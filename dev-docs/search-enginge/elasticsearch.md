---
layout: wiki
title: Elasticsearch
---
## Elasticsearch

> This page is a draft

Here is a mapping used for integration tests:


> curl -XPUT 'localhost:9200/your-index' -H 'Content-type: application/json' -d '{"mappings": {"_doc": {"properties": {"version": {"type": "keyword"}, "locale": {"type": "keyword"}, "gender": {"type": "keyword"}, "brand": {"type": "keyword"}, "style": {"type": "keyword"}, "product_id": {"type": "keyword"}, "size": {"type": "keyword"}, "color": {"type": "keyword"}, "product_group": {"type": "keyword"}, "series": {"type": "keyword"}, "price_incl_tax_en": {"type": "integer"}, "category": {"type": "text"}, "foo": {"type": "keyword"}, "id": {"type": "keyword"}, "website": {"type": "keyword"}, "bar": {"type": "keyword"}, "baz": {"type": "keyword", "copy_to": "full_text_search"}, "price": {"type": "integer"}, "full_text_search": {"type": "text"}}}}}'
