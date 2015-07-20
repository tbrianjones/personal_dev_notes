Using Elasticsearch
===================


Controlling Elasticsearch from the CLI
--------------------------------------

### See Elasticsearch Setup .MD
- [setup_elasticsearch_server_on_ec2.md](setup_elasticsearch_server_on_ec2.md#controlling-es)
- Specifically, the Controlling ES section.

### In Summary

- swtich to the "elasticsearch" user: `sudo su elasticsearch`
- start elasticsearch `ES_HEAP_SIZE=4g /usr/local/elasticsearch/bin/elasticsearch`
  - the `ES_HEAP_SIZE` cannot be pre-set without the service wrapper ( TBJ can't find where to set this )
  - so ... it must be specified at launch
  - set to half the avaialble ram on the machine
  - this setting replaces setting these individually ( `ES_MIN_MEM` & `ES_MAX_MEM` )


Recovering an Elasticsearch Index From S3
-----------------------------------------
- the current ES configurations will recover whatever index is specified in:
  - `/usr/local/elasticsearch/config/elasticsearch.yml`
  - it will do this as soon as you start elasticsearch
  - it takes about one minute per gigabyte of index size
  - *If the local hard drive is too small to contain the index, the index may become corrupted*
    - index size reported in head x2.5 is required for the hd ( what tbj has found )


Useful Queries
--------------

### quick and simple query  
- `curl -X GET "http://localhost:9200/index_name/type_name/_search?q=property_name:search_terms&pretty=true"`

### Term Query
- *does not apply a search analyzer to the query*: meaning, the search query will not be lower-cased, if the analyzer does so

```
{
  "query": {
    "term": {
      "name": "part of the company name or the company name"
    }
  }
}
```

### Query Term Query
- *query is run through the search analyzer*

```
{
  "query": {
    "query_string": {
      "default_field": "_all",
      "query": "anything"
    }
  }
}
```

### Term Filter
- *does not apply a search analyzer to the query*: meaning, the search query will not be lower-cased, if the analyzer does so

```
{
  "filter": {
    "term": {
      "name": "company name"
    }
  }
}
```

### Facets
- These can take a really long time to run on fields that have lots of values per document and where the number of potential values can be large.  eg. terms on the company document can have hundreds or thousands of values per doc and there can be millions of values.
- The above is still true, but this has been improved dramatically in V0.9+.  In V1.0+ an entirely new facetting system has been implemented.  TBJ has not experimented with this version yet.
- The most efficient way to manage facets is to store them as the ids associated with those terms.  Currently, we push the actual term into the ES index.  This should probably be changed at some point.

```
curl -X POST "http://remote_host:9200/companies/company/_search?pretty=true" -d '
{
    "query" : {
        "match_all" : {  }
    },
    "facets" : {
        "a_title_for_the_facet_when_returned" : {
            "terms" : {
                "field" : "industries.term.keyword_lowercase",
                "size" : 100
            }
        }
    },
    "size" : 0
}
'
```


### Test the Current Analyzers
- run some text through any analyzers defined in the current system:
- `curl -X GET "http://localhost:9200/index_name/_analyze?analyzer=whitespace" -d "some sample text"`
- http://www.elasticsearch.org/guide/reference/api/admin-indices-analyze/
- note that you can specify the analyzer characteristics in the URL to speed testing

### Check The Index Mapping:  
- `curl -XGET 'http://127.0.0.1:9200/my_index/_mapping?pretty=1'`

### View Analyzed Fields
- This shows how a field is actually stored in the index.

```
curl -XGET 'http://127.0.0.1:9200/my_index/_search?pretty=1'  -d '  
{
    "facets" : {
       "my_terms" : {
          "terms" : {
             "size" : 50,
             "field" : "foo"
          }
       }
    }
}
'
```

### Update Document Mapping
- Adds a field to a mapping without any downtime.
- Changing a field that has indexed data is not this simple.  Reindexing must happen.
- Docs: http://www.elasticsearch.org/guide/reference/api/admin-indices-put-mapping/
- This is a good tutorial: http://www.elasticsearch.org/blog/changing-mapping-with-zero-downtime/

```
curl -v -XPUT 'http://remote_host:9200/companies/file/_mapping' -d '
{
    "file" : {
        "properties" : {
            "terms" : { "type" : "string", "index" : "analyzed", "analyzer" : "keyword_lowercase", "include_in_all" : "true", "boost" : 2.0 }
        }
    }
}
'
```
