---
description: Elasticsearch source code description
---

# Source Code

```python
# How to run a flask. API name total_search
@totalsearch.route('/total_search', methods=['POST'])
def total_search():
    # http://daplus.net/python-%ED%94%8C%EB%9D%BC%EC%8A%A4%ED%81%AC-%EC%9A%94%EC%B2%AD%EC%97%90%EC%84%9C-%EC%88%98%EC%8B%A0-%ED%95%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8-%EC%98%A4%EA%B8%B0/
    # Need to change to match request type.
    keyword = request.args.get("keyword", False) # keyword parameter
    
    # paginations
    try:
        page_number = int(request.args.get('page', 1)) # page parameter
    except:
        page_number = 1
    
    # Search user keyword in search_term index
    def search_terms():
        body = {
            "query": {
                "bool": {
                    "must": {
                        "term": {
                            "search_term": keyword
                        }
                    }
                }
            }
        }
        
        # search search_terms index
        search_es = es.search(index='search_term', body=body)

        # if not existence keyword in search_term, save keyword in search_term index
        if search_es['hits']['total']['value'] == 0:
            # Initialization keyword and count
            doc = {
                "search_term": keyword,
                "count": 1 
            }
            # create in search_term
            es.index(index="search_term", doc_type="_doc", body=doc)

        # if existence keyword in search_term, keyword count plus 1
        else:
            id = search_es['hits']['hits'][0]['_id']
            count = search_es['hits']['hits'][0]['_source']['count']
            source_to_update = {
                "doc": {
                    "search_term": keyword,
                    "count": count + 1
                }
            }
            # update keyword information in search_term
            es.update(index="search_term", doc_type="_doc",
                      id=id, body=source_to_update)

    # paginations
    if page_number == 1:
        from_range = 0
    elif page_number >= 2:
        # processing 20 each
        from_range = (page_number-1)*20 
    
    # if not input keyword, Return all documents that meet the criteria
    if not keyword:
        body = {
            "query": {
                "bool": {
                    "must": [
                        {
                            "match_all": {}
                        }
                    ],
                    "filter": [
                        {
                            "match": {
                                "isSystem": True
                            }
                        },
                        {
                            "match": {
                                "isPrivate": False
                            }
                        }
                    ]
                }
            },
            'from': from_range,
            'size': 20
        }
    # if input keyword, Return documents containing keyword
    else:
        body = {
            "query": {
                "bool": {
                    "should": [
                        {
                            "term": {
                                # Use ngram in name,
                                # Weighted to be the first output
                                "name.ngram": {
                                    "value": keyword,
                                    "boost": 6,
                                }
                            },
                        },
                        {
                            "term": {
                                # Use ngram in categorymainName,
                                # Weighted to be the second output
                                "categoryInfo.main.categoryName.ngram": {
                                    "value": keyword,
                                    "boost": 700
                                }
                            }
                        },
                        {
                            "term": {
                                # Use ngram in categorymiddleName,
                                # Weighted to be the second output
                                "categoryInfo.middle.categoryName.ngram": {
                                    "value": keyword,
                                    "boost": 700
                                }
                            }
                        },
                        {
                            "term": {
                                # Weighted to be the third output
                                "taggedTags.tagName": {
                                    "value": keyword,
                                    "boost": 50
                                }
                            }
                        },
                        {
                            "term": {
                                # Use ngram in description,
                                # Weighted to be the last output
                                "desc.ngram": {
                                    "value": keyword,
                                    "boost": 1
                                }
                            }
                        },
                    ],
                    # Return if at least one is correct
                    "minimum_should_match": 1,
                    "filter": [
                        {
                            "match": {
                                "isSystem": True
                            }
                        },
                        {
                            "match": {
                                "isPrivate": False
                            }
                        }
                    ]
                },
            },
            # sort score with weight
            'sort': [
                {
                    "_score": {
                        "order": "desc"
                    },
                },
            ],
            'from': from_range,
            'size': 20
        }

    res = {'_id': []}
    # Search in presentations index. / if want to use scroll, add scroll
    search_result = es.search(index='presentations', body=body) # , scroll='1s'

    # pprint(search_result)
    
    # final search result return.
    for i in search_result['hits']['hits']:
        if i['_id'] not in res['_id']:
            res['_id'].append(i['_id'])

    ## scroll -- 10,000개 이상 표출할때 // 속도 조금 걸림
    ## if want infinite result, Activating the code below.
    # while len(search_result['hits']['hits']):
    #     search_result = es.scroll(
    #         scroll_id=search_result['_scroll_id'],
    #         scroll='1s'
    #     )
    #
    #     for i in search_result['hits']['hits']:
    #         if i['_id'] not in res['_id']:
    #             res['_id'].append(i['_id'])

    # search_terms()

    return jsonify(res)
```
