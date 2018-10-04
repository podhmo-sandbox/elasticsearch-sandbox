# require httpie

check-indices:
	http :9200/_cat/indices

search:
	http :9200/00unmapped
	http -b :9200/00unmapped/_search q=="title:hello"
	http -b :9200/00unmapped/_search q=="nickname:f"

setup:
	cat mapping.json | http --json PUT :9200/00unmapped
	cat data.json | http --json POST :9200/00unmapped/doc
clean:
	http DELETE :9200/00unmapped
