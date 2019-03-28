#to get the indices


curl -XGET 'localhost:9200/_cat/indices?v&pretty'
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   customer ZE6XxAg8Rdmn7_mdg0s7lQ   5   1          0            0      1.1kb          1.1kb
yellow open   products KeoLtT5WTiSM0N_bXUTzRw   5   1          0            0      1.2kb          1.2kb
yellow open   order    ql204uyNRpud9UogxVM3Rw   5   1          0            0      1.1kb          1.1kb


# to create an index
KSATYAM-M-81Z5:an ksatyam$ curl -XPUT 'localhost:9200/customer?&pretty'
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "customer"
}

#delete an index:
KSATYAM-M-81Z5:an ksatyam$ curl -XDELETE 'localhost:9200/hello?&pretty'
{
  "acknowledged" : true
}
