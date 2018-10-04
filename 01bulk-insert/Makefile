# require httpie

INDEX := 01bulk-insert

check-indices:
	http :9200/_cat/indices

setup:
	cat mapping.json | http --json PUT :9200/${INDEX}
	cat data2.json | http --verbose --json POST :9200/${INDEX}/doc/_bulk
search:
	http :9200/${INDEX}
	http :9200/${INDEX}/_search
clean:
	http DELETE :9200/${INDEX}
