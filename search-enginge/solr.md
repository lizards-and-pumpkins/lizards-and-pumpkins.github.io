---
layout: wiki
title: Solr
---
## Solr

To use Solr as search engine backend first of all you need a running Solr instance. Then the following line has to be added to a `composer.json` of your project:

```json
"lizards-and-pumpkins/lib-search-engine-solr": "..."
```

In an implementation specific factory the `createSearchEngine` method must be modified to return an instance of `SolrSearchEngine`:

```php
public function createSearchEngine()
{
    /** @var ConfigReader $configReader */
    $configReader = $this->getMasterFactory()->createConfigReader();
    $solrConnectionPath = $configReader->get('solr_connection_path');

    $solrHttpClient = new CurlSolrHttpClient($solrConnectionPath);
        
    return new SolrSearchEngine($solrHttpClient, $this->getMasterFactory()->getFacetFieldTransformationRegistry());
}
```

Finally the `LP_SOLR_CONNECTION_PATH` environment variable must to be added to both [webserver](https://github.com/lizards-and-pumpkins/catalog/wiki/Configuration#webserver) and [shell](https://github.com/lizards-and-pumpkins/catalog/wiki/Configuration#command-line) environments.  
It must be set to something like `http://localhost:8983/solr/core-name/` where "core-name" has to be replaced with the name of your Solr core. __Do NOT overlook the trailing `/`!__

In order to run Unit and Integration tests the following nodes must be present in a schema:

```xml
<fieldType name="text_tokenized" class="solr.TextField" positionIncrementGap="100">
    <analyzer>
        <tokenizer class="solr.NGramFilterFactory" minGramSize="2" maxGramSize="30"/>
        <filter class="solr.LowerCaseFilterFactory"/>
    </analyzer>
</fieldType>

<fieldType name="text_sortable" class="solr.TextField" sortMissingLast="true" multiValued="false">
    <analyzer>
        <tokenizer class="solr.KeywordTokenizerFactory"/>
        <filter class="solr.LowerCaseFilterFactory"/>
        <filter class="solr.TrimFilterFactory"/>
    </analyzer>
</fieldType>

<field name="foo" type="string" indexed="true" stored="false"/>
<field name="bar" type="string" indexed="true" stored="false"/>
<field name="baz" type="text_general" indexed="true" stored="false"/>
<field name="baz_tokenized" type="text_tokenized" indexed="true" stored="false"/>
<field name="qux" type="string" indexed="true" stored="false" multiValued="true"/>
<field name="website" type="string" indexed="true" stored="false"/>
<field name="version" type="string" indexed="true" stored="false"/>
<field name="locale" type="string" indexed="true" stored="false"/>
<field name="product_id" type="string" indexed="true" stored="true"/>
<field name="price" type="int" indexed="true" stored="true"/>

<field name="product_id_sort" type="text_sortable" indexed="true" stored="false"/>
<field name="foo_sort" type="text_sortable" indexed="true" stored="false"/>
<field name="price_sort" type="text_sortable" indexed="true" stored="false"/>

<field name="full_text_search" type="text_tokenized" indexed="true" stored="false" multiValued="true"/>

<copyField source="baz" dest="full_text_search"/>
<copyField source="product_id" dest="product_id_sort"/>
<copyField source="foo" dest="foo_sort"/>
<copyField source="price" dest="price_sort"/>
```

Each product attribute specific to your implementation must be also present in the schema.

For each attribute which is configurable as searchable (returned by `getSearchableAttributeCodes()` method of your project specific factory) must be copied to `full_text_search` field. E.g.:

```xml
<field name="name" type="string" indexed="true" stored="false"/>
<copyField source="name" dest="full_text_search"/>
```

The `full_text_search` field type declaration can be adjusted in any possible way to match business requirements.

For instance in the example above minimum token grain is set to `2` and maximum to `30`. That means that word "Uncle" will be searchable by "unc", "uncl", "ncl", "ncle" and "cle" query strings. And tokens longer than 30 characters will not be indexed.

If for example your business requirements are prescribing that search term must only match the beginning of the string you can replace `solr.NGramFilterFactory` tokenizer class with `solr.EdgeNGramFilterFactory` specifying the end of the string to match. E.g.:

```xml
<tokenizer class="solr.EdgeNGramFilterFactory" minGramSize="1" maxGramSize="15" side="front"/>
```

The example above will find string "Bob" by "b", "bo" and eventually "bob" query strings.

For more schema configuration options please refer to [Solr Documentation](https://wiki.apache.org/solr/AnalyzersTokenizersTokenFilters).

A special case in schema is price. Lizards & Pumpkins is storing final price including each possible tax in a search engine. For this purpose the following dynamic field declaration must be present in a schema:

```xml
<dynamicField name="price_incl_tax_*" type="int" indexed="true" stored="false"/>
```

Each "sortable" attribute might be accompanied by a sibling with a name suffixed with "_sort" and "text_sortable" type. Then the original attribute value must be copied to a "sortable" brother. For a reference see the test schema above.
