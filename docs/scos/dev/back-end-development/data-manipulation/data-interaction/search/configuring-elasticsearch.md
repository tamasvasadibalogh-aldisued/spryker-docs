---
title: Configuring Elasticsearch
description: Elasticsearch is a NoSQL data store which lets you predefine the structure of the data you store in it.
last_updated: Jul 24, 2022
template: howto-guide-template
originalLink: https://documentation.spryker.com/2021080/docs/search-configure-elasticsearch
originalArticleId: 6aa9f4ab-25de-46bc-b734-54bccb25cf0b
redirect_from:
  - /2021080/docs/search-configure-elasticsearch
  - /2021080/docs/en/search-configure-elasticsearch
  - /docs/search-configure-elasticsearch
  - /docs/en/search-configure-elasticsearch
  - /v6/docs/search-configure-elasticsearch
  - /v6/docs/en/search-configure-elasticsearch
  - /v5/docs/search-configure-elasticsearch
  - /v5/docs/en/search-configure-elasticsearch
  - /v4/docs/search-configure-elasticsearch
  - /v4/docs/en/search-configure-elasticsearch
  - /v3/docs/search-configure-elasticsearch
  - /v3/docs/en/search-configure-elasticsearch
  - /v2/docs/search-configure-elasticsearch
  - /v2/docs/en/search-configure-elasticsearch
  - /v1/docs/search-configure-elasticsearch
  - /v1/docs/en/search-configure-elasticsearch
  - /v5/docs/configure-elasticsearch
  - /v5/docs/en/configure-elasticsearch
  - /v4/docs/configure-elasticsearch
  - /v4/docs/en/configure-elasticsearch
  - /v6/docs/configure-elasticsearch
  - /v6/docs/en/configure-elasticsearch
  - /v3/docs/configure-elasticsearch
  - /v3/docs/en/configure-elasticsearch
  - /v2/docs/configure-elasticsearch
  - /v2/docs/en/configure-elasticsearch
  - /v6/docs/search-30
  - /v6/docs/en/search-30
  - /v6/docs/search-40
  - /v6/docs/en/search-40
  - /v5/docs/search-30
  - /v5/docs/en/search-30
  - /v5/docs/search-40
  - /v5/docs/en/search-40
  - /v4/docs/search-30
  - /v4/docs/en/search-30
  - /v4/docs/search-40
  - /v4/docs/en/search-40
  - /v3/docs/search-30
  - /v3/docs/en/search-30
  - /v3/docs/search-40
  - /v3/docs/en/search-40
  - /v2/docs/search-30
  - /v2/docs/en/search-30
  - /v2/docs/search-40
  - /v2/docs/en/search-40
related:
  - title: Configuring search for multi-currency
    link: docs/scos/dev/back-end-development/data-manipulation/data-interaction/search/configuring-search-for-multi-currency.html
  - title: Configuring the search features
    link: docs/scos/dev/back-end-development/data-manipulation/data-interaction/search/configuring-the-search-features.html
  - title: Configuring the search query
    link: docs/scos/dev/back-end-development/data-manipulation/data-interaction/search/configuring-the-search-query.html
  - title: Expanding search data
    link: docs/scos/dev/back-end-development/data-manipulation/data-interaction/search/expanding-search-data.html
  - title: Facet filter overview and configuration
    link: docs/scos/dev/back-end-development/data-manipulation/data-interaction/search/facet-filter-overview-and-configuration.html
---

Elasticsearch is a NoSQL data store that lets you predefine the structure of the data you get to store in it.

Because the used data structure is static, you need to define it in advance. The definitions of the indexes and mappings are written in JSON format. You can find it in the [official Elasticsearch documentation](https://www.elastic.co/guide/index.html).

The content of the configuration files must follow the conventions listed in the [Create index API](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html) document of the official Elasticsearch documentation. 

Note that the current search installer supports only settings and mappings. However, if you need more, you can extend it on the project level.

{% info_block infoBox "Schema example" %}

For the main index called `page`, you can find the default schema configuration in `vendor/spryker/search-elasticsearch/src/Spryker/Shared/SearchElasticsearch/Schema/page.json`.

{% endinfo_block %}

{% info_block warningBox %}

To disable the default mapping installation, override the core configuration defined in `Spryker\Zed\SearchElasticsearch\SearchElasticsearchConfig::getJsonIndexDefinitionDirectories()` by implementing it on the project level—for example, `Pyz\Zed\Search\SearchConfig`.

{% endinfo_block %}

Each configured store has its index, which is installed automatically. An index name consists of the following parts:
* An optional prefix, which is defined by the `SearchElasticsearchConstants::INDEX_PREFIX` configuration option.
* A store name.
* A configuration file name. 

Index name components are delimited with an underscore—for example, `spryker_de_page`.

## Adjust existing indexes

The following example shows how the default schema configuration file for the main index `page` can be changed to allow searching for keywords containing the special character *&* (ampersand) by switching from a [`standard`](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-standard-tokenizer.html) tokenizer to a combination of [`keyword`](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-keyword-tokenizer.html) tokenizer and token filter of the [`word_delimiter_graph`](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-word-delimiter-graph-tokenfilter.html) type.      

<details><summary markdown='span'>src/Pyz/Shared/Search/Schema/page.json</summary>

```json
{
    "settings": {
        "analysis": {
            "analyzer": {
                "fulltext_index_analyzer": {
                    "tokenizer": "keyword",
                    "filter": ["my_custom_word_delimiter_graph_filter", "lowercase", "fulltext_index_ngram_filter"]
                },
                "fulltext_search_analyzer": {
                    "tokenizer": "keyword",
                    "filter": ["custom_word_delimiter_graph_filter", "lowercase"]
                }
            },
            "filter": {
                "fulltext_index_ngram_filter": {
                    "type": "edge_ngram",
                    "min_gram": 2,
                    "max_gram": 20
                },
                "custom_word_delimiter_graph_filter": {
                    "type": "word_delimiter_graph",
                    "type_table": [ "& => ALPHA" ],
                    "split_on_case_change": false,
                    "split_on_numerics": false
                }
            }
        }
    },
    "mappings": {
        "page": {
            "properties": {
                "full-text": {
                    "analyzer": "fulltext_index_analyzer",
                    "search_analyzer": "fulltext_search_analyzer"
                },
                "full-text-boosted": {
                    "analyzer": "fulltext_index_analyzer",
                    "search_analyzer": "fulltext_search_analyzer"
                }
            }
        }
    }
}
```

</details>

For details about applying changes made to schema configuration files, see the [Install indexes and mappings](#install-indexes-and-mappings) section.

## Define new indexes and mappings

To define new indexes and mappings, under the `Shared` namespace of any module, create new configuration files—for example, `src/Shared/MyModuleName/Schema/myindex.json`.

To extend or overwrite the existing configurations, create a new file with the same name you want to modify and provide only the differences compared to the original one.

When the search installer runs, it reads all the available configuration files and merges them by an index name per store. This might be handy if you have modules that are not tightly coupled together, but both need to use the same index for some reason, or you just need to extend or override the default configuration provided on the core level.

To extend or modify indexes and mappings for a specific store, create a new configuration file along with the store's name—for example, `de_page.json`. The file is used for this store only. For example, if you have a different analyzing strategy for your stores, you must define it separately.

## Install indexes and mappings

```php
vendor/bin/console search:setup:sources
vendor/bin/console search:setup:source-map
```

The first command installs indexes that are not created and [updates the mappings](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html) based on the JSON configurations.

If an index is created with the given settings, it is changed by running this process, but the mapping can be modified and changed.

In the development environment, to create new analyzers or change the index settings, you must delete the index and run the installation process again.

To populate the newly created index with data, run `publish:trigger-events`:
```php
vendor/bin/console publish:trigger-events
```

After running the `search:setup:source-map` command, a helper class is autogenerated for every installed index. You can find these classes under the `\Generated\Shared\Search` namespace. The name of the generated class starts with the name of the index and is followed by the suffix `IndexMap`.

For the default page index, the class is `\Generated\Shared\Search\PageIndexMap`.

These classes provide some information from mapping, such as fields and metadata. Use these classes for references to program against something related to that mapping schema.

If you change mapping and run the installer, autogenerated classes change accordingly.
