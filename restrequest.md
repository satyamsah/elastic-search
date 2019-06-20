



#basic components

1. nodes
2. shards
3. replicas
4.  index
5. document type
6. document



#to get the descriptions of indices
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
curl -X DELETE "localhost:9200/product?pretty"
{
  "acknowledged" : true
}


#creating a document using PUT and ID
NEW:

curl -X PUT "localhost:9200/customer/doc/1?pretty" -H 'Content-Type: application/json' -d'
{
  "name": "John Doe"
}
'


#creating a document using WITHOUT ID using POST


curl -X POST "localhost:9200/customer/doc?pretty" -H 'Content-Type: application/json' -d'
{
  "name": "Jane Doe"
}



#fetching the document 

KSATYAM-M-81Z5:~ ksatyam$ curl -XGET "localhost:9200/products/doc/2?pretty"

OUTPUT:
{
  "_index" : "products",
  "_type" : "doc",
  "_id" : "2",
  "_version" : 2,
  "_seq_no" : 1,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "prod_type" : "mobile",
    "name" : "S9",
    "camera" : "12MP"
  }
}


#updating the document

curl -X POST "localhost:9200/customer/doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "doc": { "name": "Jane Doe" }
}
'

curl -XPOST "localhost:9200/customers/doc/1/_update?pretty" -H 'Content-Type: application/json' -d '{ "name": "JANE DOE"}'


#Difference between updating the document and modifying the data

modifying : use PUT: modifies the values of the field. 
updating: POST: the document schema gets updated. Make sure to update the values otherwise no change will occur in the schema

INPUT: 
 curl -X POST "localhost:9200/customers/doc/1/_update?pretty" -H 'Content-Type: application/json' -d'
{
  "doc": { "name": "JANE DOE", "age": 20 }
}
'
OUTPUT: 

{
  "_index" : "customers",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 5,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 4,
  "_primary_term" : 1
}


#script to change the values 

 curl -XPOST "localhost:9200/customers/doc/1/_update?pretty" -H 'Content-Type: application/json' -d '{ "script" : "ctx._source.age+=5"}'

 OUTPUT: 
{
  "_index" : "customers",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 6,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 5,
  "_primary_term" : 1
}


In the above example, ctx._source refers to the current source document that is about to be updated.




#DELETE a document


curl -X DELETE "localhost:9200/customers/doc/2?pretty"


#Batch Processing

curl -X POST "localhost:9200/customers/doc/_bulk?pretty" -H 'Content-Type: application/json' -d'
{"index": {"_id": "1"}} 
{"name": "John Doe"} 
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
'



OUTPUT:

{
  "took" : 10,
  "errors" : false,
  "items" : [
    {
      "index" : {
        "_index" : "customers",
        "_type" : "doc",
        "_id" : "1",
        "_version" : 10,
        "result" : "updated",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 9,
        "_primary_term" : 1,
        "status" : 200
      }
    },
    {
      "index" : {
        "_index" : "customers",
        "_type" : "doc",
        "_id" : "2",
        "_version" : 4,
        "result" : "updated",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 4,
        "_primary_term" : 1,
        "status" : 200
      }
    }
  ]
}




#Playing with Product
#create

curl -XPUT 'localhost:9200/products/doc/1/?pretty'  -H 'Content-Type: application/json' -d'
{
	"type":"shoe",
	"size":8,
	"color":"white"
}

OUTPUT

{
  "_index" : "products",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 2,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}


#get
curl -XGET 'localhost:9200/products/doc/1/?pretty'

OUTPUT
{
  "_index" : "products",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 2,
  "_seq_no" : 1,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "type" : "shoe",
    "size" : 8,
    "color" : "white"
  }
}

#update
curl -XPOST 'localhost:9200/products/doc/1/_update?pretty'  -H 'Content-Type: application/json' -d'
{
	"doc":{"reviews" : ["good","I love it"],"texture":"cotton"}
}'


curl -XGET 'localhost:9200/products/doc/1/?pretty'
{
  "_index" : "products",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 3,
  "_seq_no" : 2,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "type" : "shoe",
    "size" : "8",
    "color" : "white",
    "reviews" : [
      "good",
      "I love it"
    ],
    "texture" : "cotton"
  }
}


#noupdate:NOOP
curl -XPOST 'localhost:9200/products/doc/1/_update?pretty'  -H 'Content-Type: application/json' -d'
{
	"doc":{"reviews" : ["good","I love it"],"texture":"cotton"}
}'


{
  "_index" : "products",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 3,
  "result" : "noop",
  "_shards" : {
    "total" : 0,
    "successful" : 0,
    "failed" : 0
  }
}



#script
curl -XPOST 'localhost:9200/products/doc/1/_update?pretty'  -H 'Content-Type: application/json' -d'
{
	"script":"ctx._source.size+=2"
}'

#OUPTPUT
{
  "_index" : "products",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 4,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 3,
  "_primary_term" : 1
}


curl -XGET 'localhost:9200/products/doc/1/?pretty'

{
  "_index" : "products",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 6,
  "_seq_no" : 5,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "type" : "shoe",
    "size" : 10,
    "color" : "white"
  }
}


# adding a doc of shoes without ID using POST:


ksatyam$ curl -X POST "localhost:9200/products/doc?pretty"  -H 'Content-Type: application/json' -d'
{
	"name":"Adidas",
	"size":9
 }
'

{
  "_index" : "products",
  "_type" : "doc",
  "_id" : "fnPz1mkBEV3MqOK1j8ei",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}


#get the shoe with automgenerated id:

curl -X GET "localhost:9200/products/doc/fnPz1mkBEV3MqOK1j8ei?pretty"

OUTPUT:
{
  "_index" : "products",
  "_type" : "doc",
  "_id" : "fnPz1mkBEV3MqOK1j8ei",
  "_version" : 1,
  "_seq_no" : 1,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "name" : "Adidas",
    "size" : 9
  }
}


#bulk

curl -X POST "localhost:9200/products/doc/_bulk?pretty"  -H 'Content-Type: application/json' -d'
{"index":{"_id":"5"}}
{"type":"shoes","name":"steve maiden","size":9}
{"index":{"_id":"4"}}
{"type":"shoes","name":"jordan","size":10}
'




#bulk update and delete of shoes

curl -X POST "localhost:9200/products/doc/_bulk?pretty"  -H 'Content-Type: application/json' -d'
{"update":{"_id":"5"}}
{"doc": { "name":"steve maiden by clark","size":9}}
{"delete":{"_id":"2"}}
'



#upload the data using a json

curl -H "Content-Type: application/json" -XPOST 'localhost:9200/bank/account/_bulk?pretty&refresh' --data-binary "@/Users/ksatyam/Desktop/elastic-search/data/accounts.json"



curl -X GET "localhost:9200/products/doc/_search" -d'
{
  "query": {
    "match": {
      "_id": *
    }
  }
}'




#search in an Index:

curl -XGET 'localhost:9200/customers/_search?q=*&pretty'
{
  "took" : 144,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "doc",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "name" : "Jane Doe"
        }
      },
      {
        "_index" : "customers",
        "_type" : "doc",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "name" : "John Doe"
        }
      }
    ]
  }
}



ksatyam$ curl -XGET 'localhost:9200/products/_search?q=*&pretty'
{
  "took" : 16,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 5,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "products",
        "_type" : "doc",
        "_id" : "5",
        "_score" : 1.0,
        "_source" : {
          "type" : "shoes",
          "name" : "steve maiden by clark",
          "size" : 9
        }
      },
      {
        "_index" : "products",
        "_type" : "doc",
        "_id" : "4",
        "_score" : 1.0,
        "_source" : {
          "type" : "shoes",
          "name" : "jordan",
          "size" : 10
        }
      },
      {
        "_index" : "products",
        "_type" : "doc",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "type" : "shoe",
          "size" : 10,
          "color" : "white"
        }
      },
      {
        "_index" : "products",
        "_type" : "doc",
        "_id" : "fXMO1WkBEV3MqOK1RMcT",
        "_score" : 1.0,
        "_source" : {
          "prod_type" : "mobile",
          "name" : "IphoneX",
          "camera" : "10MP"
        }
      },
      {
        "_index" : "products",
        "_type" : "doc",
        "_id" : "fnPz1mkBEV3MqOK1j8ei",
        "_score" : 1.0,
        "_source" : {
          "name" : "Adidas",
          "size" : 9
        }
      }
    ]
  }
}


curl -X GET "localhost:9200/_search?pretty" -H 'Content-Type: application/json' -d'
{
    "query": {
        "match_all": {"index":"products"}
    }
}
'





#WHY THIS OUTPUT: because top 10


url -X GET "localhost:9200/customers/_search?pretty" -H 'Content-Type: application/json' -d'
{
    "query": {
        "match_all": {}              
    }
}
'



OUTPUT:


{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "doc",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "name" : "Jane Doe"
        }
      },
      {
        "_index" : "customers",
        "_type" : "doc",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "name" : "John Doe"
        }
      }
    ]
  }
}


$ query parameter


VVI: match all 

 curl -X GET "localhost:9200/products/_search?pretty" -H 'Content-Type: application/json' -d'
{
    "query": {
        "match_all": {}
    }
}
'



 curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{ "query" : {"match_all":{}},"from":5,"size":3 }'




curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query" : {"match_all":{}},
"sort": {"age":{"order":"desc"}},
"size":20
}
'
{
  "took" : 37,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : null,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "XHNg2WkBEV3MqOK1DM_I",
        "_score" : null,
        "_source" : {
          "name" : "Kidd Russell",
          "age" : 75,
          "gender" : "male",
          "email" : "kiddrussell@talkalot.com",
          "phone" : "+1 (859) 500-2229",
          "street" : "500 Powell Street",
          "city" : "Grapeview",
          "state" : "Wyoming, 8657"
        },
        "sort" : [
          75
        ]
      },
....
....

}


curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query" : {"term":{"street":"chestnut"}}
}
'
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 4.8794184,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "K3Ng2WkBEV3MqOK1DNLJ",
        "_score" : 4.8794184,
        "_source" : {
          "name" : "Irwin Walter",
          "age" : 33,
          "gender" : "male",
          "email" : "irwinwalter@talkalot.com",
          "phone" : "+1 (870) 479-3687",
          "street" : "576 Chestnut Avenue",
          "city" : "Smock",
          "state" : "Michigan, 222"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "AHNg2WkBEV3MqOK1DNDI",
        "_score" : 4.822102,
        "_source" : {
          "name" : "Maddox Fulton",
          "age" : 74,
          "gender" : "male",
          "email" : "maddoxfulton@talkalot.com",
          "phone" : "+1 (987) 426-3981",
          "street" : "842 Chestnut Street",
          "city" : "Norwood",
          "state" : "Puerto Rico, 5997"
        }
      }
    ]
  }
}



# term : it has to be exatcly present in the inveted index: relevant score 

curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"_source":false, "query" : {"term":{"street":"chestnut"}}
}
'
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 4.8794184,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "K3Ng2WkBEV3MqOK1DNLJ",
        "_score" : 4.8794184
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "AHNg2WkBEV3MqOK1DNDI",
        "_score" : 4.822102
      }
    ]
  }
}



"the source fields are not importanct, so  we need to include  or exclude fields"

 curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"_source":"st*", "query" : {"term":{"state":"washington"}}
}
'
{
  "took" : 10,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 17,
    "max_score" : 4.5707855,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "23Ng2WkBEV3MqOK1DNHJ",
        "_score" : 4.5707855,
        "_source" : {
          "street" : "616 Dwight Street",
          "state" : "Washington, 8160"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "w3Ng2WkBEV3MqOK1DNLJ",
        "_score" : 4.5707855,
        "_source" : {
          "street" : "179 Randolph Street",
          "state" : "Washington, 8808"
        }
      },
....
....
}


#no change in relevence
curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"_source":["st*","*n*"], "query" : {"term":{"state":"washington"}}
}
'
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 17,
    "max_score" : 4.5707855,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "23Ng2WkBEV3MqOK1DNHJ",
        "_score" : 4.5707855,
        "_source" : {
          "gender" : "female",
          "phone" : "+1 (963) 556-3601",
          "street" : "616 Dwight Street",
          "name" : "Christina Eaton",
          "state" : "Washington, 8160"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "w3Ng2WkBEV3MqOK1DNLJ",
        "_score" : 4.5707855,
        "_source" : {
          "gender" : "male",
          "phone" : "+1 (883) 489-2747",
          "street" : "179 Randolph Street",
          "name" : "Kelley Hubbard",
          "state" : "Washington, 8808"
        }
      },


curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"_source":{
        "includes":["st*","*n*"],
        "excludes":["*der"]
      }, 
      "query" : {"term":
                      {"state":"washington"}
                }
}

"below does not output gender as it has "der" in the last
'
{
  "took" : 4,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },



match keyowrd:

curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{ 
      "query" : {"match":
                      {"name":"webster"}
                }                       
}                                       
'                                       
{
  "took" : 24,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 4.88784,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "u3Ng2WkBEV3MqOK1DNDJ",
        "_score" : 4.88784,
        "_source" : {
          "name" : "Natasha Webster",
          "age" : 39,
          "gender" : "female",
          "email" : "natashawebster@talkalot.com",
          "phone" : "+1 (977) 448-2316",
          "street" : "459 Fleet Walk",
          "city" : "Yukon",
          "state" : "South Carolina, 5139"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "xHNg2WkBEV3MqOK1DM_I",
        "_score" : 4.882802,
        "_source" : {
          "name" : "Webster Bowers",
          "age" : 73,
          "gender" : "male",
          "email" : "websterbowers@talkalot.com",
          "phone" : "+1 (804) 574-3837",
          "street" : "495 Russell Street",
          "city" : "Catharine",
          "state" : "Utah, 8131"
        }
      }
    ]
  }
}


full text query using :

-match 
-match_phrase
-match_phrase_prefix

match keyword with phrases:




curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query":{
"match" :{
"name":{
"query" : "frank norris",
"operator" : "or"
}
}
}}'
{
  "took" : 17,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 4,
    "max_score" : 5.0017066,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "wHNg2WkBEV3MqOK1DM_I",
        "_score" : 5.0017066,
        "_source" : {
          "name" : "Frank Callahan",
          "age" : 55,
          "gender" : "male",
          "email" : "frankcallahan@talkalot.com",
          "phone" : "+1 (842) 568-2290",
          "street" : "792 Elliott Walk",
          "city" : "Naomi",
          "state" : "Mississippi, 5261"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "onNg2WkBEV3MqOK1DM_I",
        "_score" : 4.882802,
        "_source" : {
          "name" : "Oneill Frank",
          "age" : 47,
          "gender" : "male",
          "email" : "oneillfrank@talkalot.com",
          "phone" : "+1 (908) 482-3727",
          "street" : "603 Clove Road",
          "city" : "Fulford",
          "state" : "Maine, 5214"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "u3Ng2WkBEV3MqOK1DM_I",
        "_score" : 4.882802,
        "_source" : {
          "name" : "Norris Whitney",
          "age" : 70,
          "gender" : "male",
          "email" : "norriswhitney@talkalot.com",
          "phone" : "+1 (854) 514-2855",
          "street" : "183 Rockwell Place",
          "city" : "Orviston",
          "state" : "West Virginia, 2238"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "y3Ng2WkBEV3MqOK1DM_I",
        "_score" : 4.8256435,
        "_source" : {
          "name" : "Jeannine Norris",
          "age" : 67,
          "gender" : "female",
          "email" : "jeanninenorris@talkalot.com",
          "phone" : "+1 (981) 512-2005",
          "street" : "648 Aitken Place",
          "city" : "Escondida",
          "state" : "North Carolina, 2443"
        }
      }
    ]
  }
}





curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query":{
"match" :{
"name":{
"query" : "frank norris",
"operator" : "or"
}
}
}}'


this signifies all the docs within the "name" field which is having value as "frnak" or" "morris"









curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query":{
"match" :{
"street":{
"query" : "tomkins place"
}                
}
}}'



{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 181,
    "max_score" : 1.989445,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "UnNg2WkBEV3MqOK1DM_I",
        "_score" : 1.989445,
        "_source" : {
          "name" : "Bell Fitzgerald",
          "age" : 35,
          "gender" : "male",
          "email" : "bellfitzgerald@talkalot.com",
          "phone" : "+1 (958) 567-2131",
          "street" : "173 Tompkins Place",
          "city" : "Epworth",
          "state" : "Puerto Rico, 2262"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "WXNg2WkBEV3MqOK1DM_I",
        "_score" : 1.989445,
        "_source" : {
          "name" : "Steele Sweeney",
          "age" : 59,
          "gender" : "male",
          "email" : "steelesweeney@talkalot.com",
          "phone" : "+1 (927) 422-2182",
          "street" : "893 Cox Place",
          "city" : "Windsor",
          "state" : "Ohio, 597"
        }
      },




curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query":{
"match_phrase" :{"street": "tompkins place"}
}
}'

it tries to "match exect phrase" in the street course . It matches sentense and quotes" 



{
  "took" : 13,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 6.991152,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "UnNg2WkBEV3MqOK1DM_I",
        "_score" : 6.991152,
        "_source" : {
          "name" : "Bell Fitzgerald",
          "age" : 35,
          "gender" : "male",
          "email" : "bellfitzgerald@talkalot.com",
          "phone" : "+1 (958) 567-2131",
          "street" : "173 Tompkins Place",
          "city" : "Epworth",
          "state" : "Puerto Rico, 2262"
        }
      }
    ]
  }
}




 curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query":{
"match_phrase" :{"state": "puerto rico"}
}
}'
{
  "took" : 7,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },





#prefix : document whihc match the prefix of "ma" like: where you don;t know the whole 



KSATYAM-M-81Z5:bin ksatyam$ curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query":{
"match_phrase_prefix" :{"name":"ma "}
}
}'




KSATYAM-M-81Z5:bin ksatyam$ curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query":{
"match_phrase_prefix" :{"name":"ma "}
}
}'
{
  "took" : 14,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 63,
    "max_score" : 9.765604,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "0nNg2WkBEV3MqOK1DNLJ",
        "_score" : 9.765604,
        "_source" : {
          "name" : "Marilyn Maxwell",
          "age" : 57,
          "gender" : "female",
          "email" : "marilynmaxwell@talkalot.com",
          "phone" : "+1 (822) 561-3235",
          "street" : "834 Carlton Avenue",
          "city" : "Hinsdale",
          "state" : "Kentucky, 9577"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "mnNg2WkBEV3MqOK1DM_I",
        "_score" : 5.0017066,
        "_source" : {
          "name" : "Marissa England",
          "age" : 36,
          "gender" : "female",
          "email" : "marissaengland@talkalot.com",
          "phone" : "+1 (960) 526-2516",
          "street" : "647 Harkness Avenue",
          "city" : "Rockingham",
          "state" : "Missouri, 8782"
        }
      },


      curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query":{
"match_phrase_prefix" :{"street":"clymer st "}
}
}'
{
  "took" : 4,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 25.301674,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "mnNg2WkBEV3MqOK1DNLJ",
        "_score" : 25.301674,
        "_source" : {
          "name" : "Hester Woods",
          "age" : 68,
          "gender" : "female",
          "email" : "hesterwoods@talkalot.com",
          "phone" : "+1 (968) 591-2846",
          "street" : "564 Clymer Street",
          "city" : "Marion",
          "state" : "Idaho, 3519"
        }
      }
    ]
  }
}


curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'{
  

}





_score: 

query clause -> relvance _score  that a particular doc get

same document has differenct relevance score when the different clause is put

Once the search is performed the ES calculate the relavant score before rretirivnb the result

ES prfomce fuzzy search, 

Term search might look at the percentage of search term 


The TF/ IDF 


the word appears in the shorter sentense is more relavnt than words which appear in long sentense



 curl -XGET  'localhost:9200/customers/_search?pretty' -H 'Content-Type: application/json'  -d'
{
"query":{
"common" :{"reviews":{"query":"thi is great","cutoff_frequency":0.001}}
}
}'






curl -XGET 'localhost:9200/customers/_search?pretty'  -H 'Content-Type: application/json' -d'{     
"query":{
         "bool" :{
                 "must" : [                         
                     { "match":{"street":"ditmas"}},
                     { "match":{ "street" :"avenue"}}
                  ]
           }
   }
}'



"and operation"
{
  "took" : 15,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 6.4453754,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "BHNg2WkBEV3MqOK1DNLJ",
        "_score" : 6.4453754,
        "_source" : {
          "name" : "Alejandra Talley",
          "age" : 25,
          "gender" : "female",
          "email" : "alejandratalley@talkalot.com",
          "phone" : "+1 (855) 503-2045",
          "street" : "334 Ditmas Avenue",
          "city" : "Osage",
          "state" : "Mississippi, 6157"
        }
      }
    ]
  }
}


boolean : Or 


curl -XGET 'localhost:9200/customers/_search?pretty'  -H 'Content-Type: application/json' -d'{
"query":{
         "bool" :{
                 "should" : [
                     { "match":{"street":"ditmas"}},
                     { "match":{ "street" :"avenue"}}
                  ]
           }
   }
}'
{
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "XXNg2WkBEV3MqOK1DM_I",
        "_score" : 1.6959926,
        "_source" : {
          "name" : "Williamson Pope",
          "age" : 55,
          "gender" : "male",
          "email" : "williamsonpope@talkalot.com",
          "phone" : "+1 (993) 456-2603",
          "street" : "761 Meserole Avenue",
          "city" : "Muse",
          "state" : "Washington, 1988"
        }
      },



boolean : NOT 


curl -XGET 'localhost:9200/customers/_search?pretty'  -H 'Content-Type: application/json' -d'{
"query":{
         "bool" :{
                 "must_not" : [
                     { "match":{"state":"california texas"}},
                     { "match":{ "street" :"lane street"}}
                  ]
           }
   }
}'
{
  "took" : 23,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 580,
    "max_score" : 1.0,
    "hits" : [
      {
...
....









curl -XGET 'localhost:9200/customers/_search?pretty'  -H 'Content-Type: application/json' -d'{
"query":{
         "bool" :{
                 "should" : [
                     { "term":{"state":{"value":"idaho"}}},
                     { "term":{ "state" :{"value":"california"}}}
                  ]
           }
   }
}'
{
  "took" : 7,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 34,
    "max_score" : 5.111915,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "fnNg2WkBEV3MqOK1DNHJ",
        "_score" : 5.111915,
        "_source" : {
          "name" : "Ophelia Schroeder",
          "age" : 70,
          "gender" : "female",
          "email" : "opheliaschroeder@talkalot.com",
          "phone" : "+1 (950) 550-3898",
          "street" : "188 Elm Avenue",
          "city" : "Kylertown",
          "state" : "California, 111"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "4nNg2WkBEV3MqOK1DM_I",
        "_score" : 4.446704,
        "_source" : {
          "name" : "Tia Stanton",
          "age" : 68,
          "gender" : "female",
          "email" : "tiastanton@talkalot.com",
          "phone" : "+1 (880) 449-3532",
          "street" : "324 Bayview Place",
          "city" : "Elbert",
          "state" : "Idaho, 7975"
        }
      },
....




# filter: 



 curl -XGET 'localhost:9200/customers/_search?pretty'  -H 'Content-Type: application/json' -d'{
"query":{
         "bool" :{
                 "must" :  
                     { "term":{"gender":"female"}},"filter":[{"term":{"gender":"female"}},{"range":{"age":{"gte":50}}}]                                   
           }                                           
   }      
}'
{
  "took" : 12,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 209,
    "max_score" : 0.8052645,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "VnNg2WkBEV3MqOK1DM_I",
        "_score" : 0.8052645,
        "_source" : {
          "name" : "Imelda Mcdaniel",
          "age" : 68,
          "gender" : "female",
          "email" : "imeldamcdaniel@talkalot.com",
          "phone" : "+1 (856) 480-3825",
          "street" : "443 Kent Street",
          "city" : "Limestone",
          "state" : "Minnesota, 6713"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "zHNg2WkBEV3MqOK1DM_I",
        "_score" : 0.8052645,
        "_source" : {
          "name" : "Roberta Alvarez",
          "age" : 68,
          "gender" : "female",
          "email" : "robertaalvarez@talkalot.com",
          "phone" : "+1 (899) 576-2615",
          "street" : "663 Dahl Court",
          "city" : "Sunriver",
          "state" : "Oklahoma, 3558"
        }
      },








      curl -XGET 'localhost:9200/customers/_search?pretty'  -H 'Content-Type: application/json' -d'{
"query":{
         "bool" :{
                 "should" : [
                     { "term":{"state":{"value":"idaho","boost":2.0}}},
                     { "term":{ "state" :{"value":"california"}}}
                  ]
           }
   }
}'
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 34,
    "max_score" : 8.893408,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "4nNg2WkBEV3MqOK1DM_I",
        "_score" : 8.893408,
        "_source" : {
          "name" : "Tia Stanton",
          "age" : 68,
          "gender" : "female",
          "email" : "tiastanton@talkalot.com",
          "phone" : "+1 (880) 449-3532",
          "street" : "324 Bayview Place",
          "city" : "Elbert",
          "state" : "Idaho, 7975"
        }
      },






url -XGET 'localhost:9200/customers/_search?pretty'  -H 'Content-Type: application/json' -d'{
"query":{
         "bool" :{
                 "must" : 
                     { "term":{"gender":"female"}},"filter":[{"term":{"gender":"female"}},{"range":{"age":{"gte":50}}}]           
           }
   }
}'
{
  "took" : 6,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 209,
    "max_score" : 0.8052645,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "VnNg2WkBEV3MqOK1DM_I",
        "_score" : 0.8052645,
        "_source" : {
          "name" : "Imelda Mcdaniel",
          "age" : 68,
          "gender" : "female",
          "email" : "imeldamcdaniel@talkalot.com",
          "phone" : "+1 (856) 480-3825",
          "street" : "443 Kent Street",
          "city" : "Limestone",
          "state" : "Minnesota, 6713"
        }
      },
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "zHNg2WkBEV3MqOK1DM_I",
        "_score" : 0.8052645,
        "_source" : {
          "name" : "Roberta Alvarez",
          "age" : 68,
          "gender" : "female",
          "email" : "robertaalvarez@talkalot.com",
          "phone" : "+1 (899) 576-2615",
          "street" : "663 Dahl Court",
          "city" : "Sunriver",
          "state" : "Oklahoma, 3558"
        }
      },
....



mrtric: sum avg count , min , max etc


avg:

multi-value stats aggregation

curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
>   "size": 0,
>   "aggs" :{
>     "agg_avg":{
>         "avg":{
>             "field" : "age"
>         }
>     }
>   }
> }
> '




output:


{
  "took" : 72,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "agg_avg" : {
      "value" : 47.082
    }
  }
}






#aggreagte

curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size": 1, //size 
  "aggs" :{
    "agg_avg":{
        "avg":{
            "field" : "age"
        }
    }
  }
}
'
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "customers",
        "_type" : "personal",
        "_id" : "UnNg2WkBEV3MqOK1DM_I",
        "_score" : 1.0,
        "_source" : {
          "name" : "Bell Fitzgerald",
          "age" : 35,
          "gender" : "male",
          "email" : "bellfitzgerald@talkalot.com",
          "phone" : "+1 (958) 567-2131",
          "street" : "173 Tompkins Place",
          "city" : "Epworth",
          "state" : "Puerto Rico, 2262"
        }
      }
    ]
  },
  "aggregations" : {
    "agg_avg" : {
      "value" : 47.082
    }
  }
}





#metrcu aggregattion with search query

curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{


"size" :0,
"query":{
    "bool":{
      "filter":{
          "match":{"state":"minnesota"}
    }
  }
},
"aggs" :{
  "avg_age":{
  "avg":{"field":"age"}
}
}

}
'

#output:

{
  "took" : 4,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 18,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "avg_age" : {
      "value" : 53.05555555555556
    }
  }
}



#stats
curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "age_stats":{
        "stats":{"field":"age"}
    }
  }
}'


{
  "took" : 10,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "age_stats" : {
      "count" : 1000,
      "min" : 18.0,
      "max" : 75.0,
      "avg" : 47.082,
      "sum" : 47082.0
    }
  }
}




#metrci aggragationm : cardinality of a flied: the  number of unique valus that is stored in a field accross documents. Enable caridnatlity agg of text field requires a special set in the "fielddata".

cardinality : the number of uniques values under that field across all the docs


curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "age_count":{
      "cardinality":{
        "field":"age"
      }
    }
  }
}
}'

output:

{
  "took" : 20,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "age_count" : {
      "value" : 58
    }
  }
}




curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "gender_count":{
      "cardinality":{
        "field":"gender"
      }
    }
  }
}
}'



#Error:

{
  "error" : {
    "root_cause" : [
      {
        "type" : "illegal_argument_exception",
        "reason" : "Fielddata is disabled on text fields by default. Set fielddata=true on [gender] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory. Alternatively use a keyword field instead."
      }
    ],
    "type" : "search_phase_execution_exception",
    "reason" : "all shards failed",
    "phase" : "query",
    "grouped" : true,
    "failed_shards" : [
      {
        "shard" : 0,
        "index" : "customers",
        "node" : "YJpIefo1Rs-KJ0b2vAL1PQ",
        "reason" : {
          "type" : "illegal_argument_exception",
          "reason" : "Fielddata is disabled on text fields by default. Set fielddata=true on [gender] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory. Alternatively use a keyword field instead."
        }
      }
    ],
    "caused_by" : {
      "type" : "illegal_argument_exception",
      "reason" : "Fielddata is disabled on text fields by default. Set fielddata=true on [gender] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory. Alternatively use a keyword field instead.",
      "caused_by" : {
        "type" : "illegal_argument_exception",
        "reason" : "Fielddata is disabled on text fields by default. Set fielddata=true on [gender] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory. Alternatively use a keyword field instead."
      }
    }
  },
  "status" : 400
}


FIELDDATA: text field(within index document) values are stored in an in-memory data structure valled fielddata

The field data data structure on demmand on fly when a field is used for aggregation or soring. 


Fielddata on text fields takes up alot of text fields. This field is turned off . Think carefully if u really need field data.


curl -XPOST 'localhost:9200/customers/_mapping/personal?&pretty' -H 'Content-Type: application/json' -d '{
  "properties":{
    "gender":{
      "type":"text",
      "fielddata":true
    }
  }
}
'



Output:

{
  "acknowledged" : true
}




ksatyam$ curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "gender_count":{
      "cardinality":{
        "field":"gender"
      }
    }
  }
}
}'
{
  "took" : 39,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "gender_count" : {
      "value" : 2
    }
  }
}








#bucketing aggregation: the totals counts of unique field values
curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "gender_bucket":{
        "terms":{
          "field":"gender"
        }
    }
  }
}


output:

{
  "took" : 26,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "gender_bucket" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "male",
          "doc_count" : 516
        },
        {
          "key" : "female",
          "doc_count" : 484
        }
      ]
    }
  }
}



#age bucket:

curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "age_bucket":{
        "terms":{
          "field":"age"
        }
    }
  }
}'

#doc_count_error_uppre_bound: 18,  #docu count can be off no more than 18
"sum_other_doc_count" : totla number if docs
{
  "took" : 15,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "age_bucket" : {
      "doc_count_error_upper_bound" : 19,
      "sum_other_doc_count" : 805,
      "buckets" : [
        {
          "key" : 49,
          "doc_count" : 22
        },
        {
          "key" : 72,
          "doc_count" : 22
        },
        {
          "key" : 70,
          "doc_count" : 21
        },
        {
          "key" : 73,
          "doc_count" : 21
        },
        {
          "key" : 34,
          "doc_count" : 20
        },
        {
          "key" : 33,
          "doc_count" : 19
        },
        {
          "key" : 59,
          "doc_count" : 19
        },
        {
          "key" : 20,
          "doc_count" : 17
        },
        {
          "key" : 31,
          "doc_count" : 17
        },
        {
          "key" : 35,
          "doc_count" : 17
        }
      ]
    }
  }
}


#bucketing using range queries:

curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "age_range":{
      "range":{
          "field":"age",
          "ranges":[
            {"to":30},
            {"from":30, "to":40},
            {"from":40, "to":55},
            {"from":55}
          ]
      }
    }
  }
}'

#   {"to":30}, , from lowest to 30 

 {"from":55}: from 55 to largtest



 {
  "took" : 4,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "age_range" : {
      "buckets" : [
        {
          "key" : "*-30.0",
          "to" : 30.0,
          "doc_count" : 199
        },
        {
          "key" : "30.0-40.0",
          "from" : 30.0,
          "to" : 40.0,
          "doc_count" : 174
        },
        {
          "key" : "40.0-55.0",
          "from" : 40.0,
          "to" : 55.0,
          "doc_count" : 253
        },
        {
          "key" : "55.0-*",
          "from" : 55.0,
          "doc_count" : 374
        }
      ]
    }
  }
}




curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "age_range":{
      "range":{
          "field":"age",
          "keyed":true,
          "ranges":[
            {"key":"young","to":30},
            {"key":"quarter-age","from":30, "to":40},
            {"key":"middle-age","from":40, "to":55},
            {"key":"senior", "from":55}
          ]
      }
    }
  }
}'


{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "age_range" : {
      "buckets" : {
        "young" : {
          "to" : 30.0,
          "doc_count" : 199
        },
        "quarter-age" : {
          "from" : 30.0,
          "to" : 40.0,
          "doc_count" : 174
        },
        "middle-age" : {
          "from" : 40.0,
          "to" : 55.0,
          "doc_count" : 253
        },
        "senior" : {
          "from" : 55.0,
          "doc_count" : 374
        }
      }
    }
  }
}




#multi-level aggregation:


curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "gender_avg_agg":{
      "terms":{"field":"gender"},
      "aggs":{
        "avg_age":{
          "avg":{
            "field":"age"
          }
        }
      }
    }
  }
}'


Output:

{
  "took" : 13,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "gender_avg_agg" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "male",
          "doc_count" : 516,
          "avg_age" : {
            "value" : 47.65503875968992
          }
        },
        {
          "key" : "female",
          "doc_count" : 484,
          "avg_age" : {
            "value" : 46.47107438016529
          }
        }
      ]
    }
  }
}



curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "gender_avg_agg":{
      "terms":{"field":"gender"},
      "aggs":{
        "age_ranges":{
          "range":{
            "field":"age",
            "keyed":true,
            "ranges":[
              {"key":"young","to":30},
              {"key":"quarter-age","from":30, "to":40},
              {"key":"middle-age","from":40, "to":55},
              {"key":"senior", "from":55}
            ]
          },
          "aggs":{
            "average_age":{
              "avg":{
                 "field":"age"
              }
            }
          }
        }
      }
    }
  }
}'


Output:
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "gender_avg_agg" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "male",
          "doc_count" : 516,
          "age_ranges" : {
            "buckets" : {
              "young" : {
                "to" : 30.0,
                "doc_count" : 105,
                "average_age" : {
                  "value" : 23.38095238095238
                }
              },
              "quarter-age" : {
                "from" : 30.0,
                "to" : 40.0,
                "doc_count" : 84,
                "average_age" : {
                  "value" : 34.25
                }
              },
              "middle-age" : {
                "from" : 40.0,
                "to" : 55.0,
                "doc_count" : 121,
                "average_age" : {
                  "value" : 47.47933884297521
                }
              },
              "senior" : {
                "from" : 55.0,
                "doc_count" : 206,
                "average_age" : {
                  "value" : 65.59708737864078
                }
              }
            }
          }
        },
        {
          "key" : "female",
          "doc_count" : 484,
          "age_ranges" : {
            "buckets" : {
              "young" : {
                "to" : 30.0,
                "doc_count" : 94,
                "average_age" : {
                  "value" : 23.28723404255319
                }
              },
              "quarter-age" : {
                "from" : 30.0,
                "to" : 40.0,
                "doc_count" : 90,
                "average_age" : {
                  "value" : 34.68888888888889
                }
              },
              "middle-age" : {
                "from" : 40.0,
                "to" : 55.0,
                "doc_count" : 132,
                "average_age" : {
                  "value" : 46.89393939393939
                }
              },
              "senior" : {
                "from" : 55.0,
                "doc_count" : 168,
                "average_age" : {
                  "value" : 65.42261904761905
                }
              }
            }
          }
        }
      ]
    }
  }
}





"filter aggregation":
curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "aggs":{
    "texas_avg_age_filter":{
      "filter":{"term":{"state":"texas"}},
      "aggs":{
        "avg_age":{
          "avg":{
            "field":"age"
          }
        }
      }
    }
  }
}'


 curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{"size":0,
  "aggs":{           
    "texas_avg_age_filter":{
      "filter":{"term":{"state":"texas"}},
      "aggs":{
        "avg_age":{
          "avg":{
            "field":"age"
          }
        }
      }
    }
  } 
}'
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "texas_avg_age_filter" : {
      "doc_count" : 17,
      "avg_age" : {
        "value" : 51.11764705882353
      }
    }
  }
}


#filters keyword : aggregation: define a mutilbucket(belows each mentioned state is a bucket), each bucket will collect all its docs and return the count of doc which matches its associated sfilter

curl -XPOST 'localhost:9200/customers/_search?&pretty' -H 'Content-Type: application/json' -d '{
  "size":0,
  "aggs":{
    "states_cnt":{
      "filters":{
        "filters":{
          "washington":{"match":{"state":"washington"}},
          "north carolina":{"match":{"state":"north carolina"}},
          "south dakota":{"match":{"state":"south dakota"}}
        }
      }
    } 
  }  
}'


output:

{
  "took" : 5,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "states_cnt" : {
      "buckets" : {
        "north carolina" : {
          "doc_count" : 53
        },
        "south dakota" : {
          "doc_count" : 52
        },
        "washington" : {
          "doc_count" : 17
        }
      }
    }
  }
}










