## ElasticSearch

Some tips to work with ElaticSearch using its REST interface.

In the examples below, let's assume that the url of the ElasticSearch instance is `https://elasticurl`. Remember that the full format for these urls, when they are protected by simple HTTP authentication (user and password), and are accessible through a non-root resource, is something like:

```
https://user:passwd@host:port/resource
```

### Working with indexes

To list all indexex stored by ElasticSearch:

```
$ curl -XGET 'https://elasticurl/_cat/indices?v'
```

This returns for each index, its name, status (`open` comes to mean 'usable'), number of documents, deleted documents, and storage size used.

### Working with aliases

ElasticSearch index aliases allow to work with a collection of indexes as if it were just a single index, or with a single index under a different name. In the following, we were refer as 'base indexes' to the real indexes for which we will create aliases. Find [more information about working with aliases in the ElasticSearch manual](https://www.elastic.co/guide/en/elasticsearch/guide/current/index-aliases.html).

To list the base indexes corresponding to an index alias (assume the index alias is `alias_index`):

```
$ curl -XGET  'https://elastic_url/alias_index/_alias/*'
```

The result will be similar to (being `base_index` the base index for the alias, and `alias_index`, `alias_index2` two aliases for that base index):

```
{
  "base_index" : {
    "aliases" : {
      "alias_index" : { },
      "alias_index2" : { }
    }
  }
}
```

To remove aliases, and create new ones, in an atomic operation:

```
curl -XPOST 'https://elastic_url/_aliases' -d '
{
    "actions" : [
        { "add" : { "index" : "base_index", "alias" : "alias_index" } },
        { "add" : { "index" : "base_index2", "alias" : "alias_index" } },
        { "remove" : { "index" : "old_base_index", "alias" : "alias_index" } },
        { "remove" : { "index" : "old_base_index", "alias" : "alias_index" } }
    ]
}'
```