# Elasticsearch REST API Cheatsheet

##### References
- [Basic URI search API docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-uri-request.html)
- [Human readable output (cat) API docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html)
- [Other (read: more advanced) search API docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html)

##### Common tasks
- Show list of indexes:
  - `/_cat/indices?v`
- Show detailed info about an index, including mappings (document schema):
  - `/<index>`
- Get some documents from an index:
  - `/<index>/_search?pretty`
- Get some documents of a particular type:
  - `/<index>/<doc_type>/_search?pretty`
  - ex. `/content/feed/_search?pretty`
- Get a particular document:
  - `/<index>/<doc_type>/<doc_id>`
  - ex. `/content/feed/XITj2qNS?pretty`
- Search for docs with field=some_value
  - `/<index>/<doc_type>/_search?pretty&q=<field_name>:<some_value>`
  - ex. `/content/media/_search?pretty&q=status:READY`
  - ex. `/content/media/_search?pretty&q=title:"Jaime Allen"`
  - ex. `/content/media/_search?pretty&q=status:READY and title:"Jaime Allen"`
  - ex. `/content/media/_search?pretty&q=(status:READY AND title:"Jaime Allen")`
  - ex. `/content/media/_search?pretty&q=source.type:FILE`
  - ex. `/content/media/_search?pretty&q=source.type:FILE`
- Search for docs where field is not null
  - `/<index>/<doc_type>/_search?pretty&q=_exists_:<field_name>`
  - ex. `/content/media/_search?pretty&q=_exists_:arm_media`
- Search for docs where field is null
  - `/<index>/<doc_type>/_search?pretty&q=-<field_name>`
  - ex. `/content/media/_search?pretty&q:-arm_media`
  - ex. `/content/media/_search?pretty&q:(_exists_:ewma_score AND -arm_media)`
  - ex. `/content/feed/_search?pretty&q:(-link AND type:MANUAL AND status:ACTIVE)`
