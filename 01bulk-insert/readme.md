## bulk insertの方法毎回調べるのでまとめておく

- [document](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)

```console
$ cat mapping.json | http --json PUT :9200/01bulk-insert
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 80
content-type: application/json; charset=UTF-8

{
    "acknowledged": true,
    "index": "01bulk-insert",
    "shards_acknowledged": true
}

$ cat data2.json | http --verbose --json POST :9200/01bulk-insert/doc/_bulk
POST /01bulk-insert/doc/_bulk HTTP/1.1
Accept: application/json, */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Content-Length: 200
Content-Type: application/json
Host: localhost:9200
User-Agent: HTTPie/0.9.9

{ "index" : {"_id" : "1" } }
{ "field1" : "value1" }
{ "delete" : {"_id" : "2" } }
{ "create" : {"_id" : "3" } }
{ "field1" : "value3" }
{ "update" : {"_id" : "1"} }
{ "doc" : {"field2" : "value2"} }

HTTP/1.1 200 OK
content-encoding: gzip
content-length: 239
content-type: application/json; charset=UTF-8

{
    "errors": false,
    "items": [
        {
            "index": {
                "_id": "1",
                "_index": "01bulk-insert",
                "_shards": {
                    "failed": 0,
                    "successful": 1,
                    "total": 1
                },
                "_type": "doc",
                "_version": 1,
                "created": true,
                "result": "created",
                "status": 201
            }
        },
        {
            "delete": {
                "_id": "2",
                "_index": "01bulk-insert",
                "_shards": {
                    "failed": 0,
                    "successful": 1,
                    "total": 1
                },
                "_type": "doc",
                "_version": 1,
                "found": false,
                "result": "not_found",
                "status": 404
            }
        },
        {
            "create": {
                "_id": "3",
                "_index": "01bulk-insert",
                "_shards": {
                    "failed": 0,
                    "successful": 1,
                    "total": 1
                },
                "_type": "doc",
                "_version": 1,
                "created": true,
                "result": "created",
                "status": 201
            }
        },
        {
            "update": {
                "_id": "1",
                "_index": "01bulk-insert",
                "_shards": {
                    "failed": 0,
                    "successful": 1,
                    "total": 1
                },
                "_type": "doc",
                "_version": 2,
                "result": "updated",
                "status": 200
            }
        }
    ],
    "took": 221
}
```

```console
$ http :9200/01bulk-insert
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 300
content-type: application/json; charset=UTF-8

{
    "01bulk-insert": {
        "aliases": {},
        "mappings": {
            "doc": {
                "properties": {
                    "field1": {
                        "fields": {
                            "keyword": {
                                "ignore_above": 256,
                                "type": "keyword"
                            }
                        },
                        "type": "text"
                    },
                    "field2": {
                        "fields": {
                            "keyword": {
                                "ignore_above": 256,
                                "type": "keyword"
                            }
                        },
                        "type": "text"
                    }
                }
            }
        },
        "settings": {
            "index": {
                "creation_date": "1538626048576",
                "number_of_replicas": "0",
                "number_of_shards": "1",
                "provided_name": "01bulk-insert",
                "refresh_interval": "1s",
                "requests": {
                    "cache": {
                        "enable": "true"
                    }
                },
                "uuid": "eRYlB81eTbisIjbvzOkD5g",
                "version": {
                    "created": "5061299"
                }
            }
        }
    }
}

$ http :9200/01bulk-insert/_search
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 205
content-type: application/json; charset=UTF-8

{
    "_shards": {
        "failed": 0,
        "skipped": 0,
        "successful": 1,
        "total": 1
    },
    "hits": {
        "hits": [
            {
                "_id": "3",
                "_index": "01bulk-insert",
                "_score": 1.0,
                "_source": {
                    "field1": "value3"
                },
                "_type": "doc"
            },
            {
                "_id": "1",
                "_index": "01bulk-insert",
                "_score": 1.0,
                "_source": {
                    "field1": "value1",
                    "field2": "value2"
                },
                "_type": "doc"
            }
        ],
        "max_score": 1.0,
        "total": 2
    },
    "timed_out": false,
    "took": 2
}
```
