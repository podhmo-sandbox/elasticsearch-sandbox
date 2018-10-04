## mappingに存在しないデータを入れてみる

mappingに存在しないデータは検索条件に指定しても捨てられる。
(たまにmapppingの設定をミスってmappingだけtypoしていることがある)

※ defaultだとdynamic mappingが有効だけれど。dynamic mappingをfalseにした場合の話。

- nicknameはデータにしか無い
- nicknameはmappingに存在しない

```console
$ cat mapping.json | http --json PUT :9200/00unmapped
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 76
content-type: application/json; charset=UTF-8

{
    "acknowledged": true,
    "index": "00unmapped",
    "shards_acknowledged": true
}

$ cat data.json | http --json POST :9200/00unmapped/doc
HTTP/1.1 201 Created
Location: /00unmapped/doc/AWY9FcjUypzT1w_NAlJi
content-encoding: gzip
content-length: 154
content-type: application/json; charset=UTF-8

{
    "_id": "AWY9FcjUypzT1w_NAlJi",
    "_index": "00unmapped",
    "_shards": {
        "failed": 0,
        "successful": 1,
        "total": 1
    },
    "_type": "doc",
    "_version": 1,
    "created": true,
    "result": "created"
}
```

利用

```console
$ http :9200/00unmapped
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 296
content-type: application/json; charset=UTF-8

{
    "00unmapped": {
        "aliases": {},
        "mappings": {
            "doc": {
                "_all": {
                    "enabled": false
                },
                "dynamic": "false",
                "properties": {
                    "age": {
                        "type": "integer"
                    },
                    "name": {
                        "type": "keyword"
                    },
                    "title": {
                        "type": "text"
                    }
                }
            }
        },
        "settings": {
            "index": {
                "creation_date": "1538623127744",
                "number_of_replicas": "0",
                "number_of_shards": "1",
                "provided_name": "00unmapped",
                "refresh_interval": "1s",
                "requests": {
                    "cache": {
                        "enable": "true"
                    }
                },
                "uuid": "T6TL_k_gTHSCzCb8tUcdzw",
                "version": {
                    "created": "5061299"
                }
            }
        }
    }
}

$ http -b :9200/00unmapped/_search q=="title:hello"
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
                "_id": "AWY9FcjUypzT1w_NAlJi",
                "_index": "00unmapped",
                "_score": 0.2876821,
                "_source": {
                    "age": 20,
                    "name": "foo",
                    "nickname": "F",
                    "title": "hello"
                },
                "_type": "doc"
            }
        ],
        "max_score": 0.2876821,
        "total": 1
    },
    "timed_out": false,
    "took": 1
}

$ http -b :9200/00unmapped/_search q=="nickname:f"
{
    "_shards": {
        "failed": 0,
        "skipped": 0,
        "successful": 1,
        "total": 1
    },
    "hits": {
        "hits": [],
        "max_score": null,
        "total": 0
    },
    "timed_out": false,
    "took": 0
}
```
