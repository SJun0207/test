---
description: Elasticsearch source code description
---

# Source Code

```python
# How to run a flask. API name category_search
@categorysearch.route('/category_search', methods=['POST'])
def category_search():
    # http://daplus.net/python-%ED%94%8C%EB%9D%BC%EC%8A%A4%ED%81%AC-%EC%9A%94%EC%B2%AD%EC%97%90%EC%84%9C-%EC%88%98%EC%8B%A0-%ED%95%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8-%EC%98%A4%EA%B8%B0/
    # Need to change to match request type.
    main = request.args.get("main", False) # main category
    middle = request.args.get('middle', False) # middle category
    price_range = request.args.get('price', False) # price setting
    mood = request.args.get('mood', False) # mood
    rating = request.args.get('rating', False)

    # price range setting
    if not price_range: # if not price range or price is zero 
        range = {
            'gte': 0
        }
    else:
        # if one price are entered,
        price_list = price_range.split(',')
        if len(price_list) == 1:
            range = {
                'gte': 0,  # gte: more than / gt: excess
                'lte': price_range  # lte: less than/ lt: under
            }
        # If two prices are entered,
        elif len(price_list) == 2:
            range = {
                'gte': price_list[0].strip(),
                'lte': price_list[1].strip()
            }

    # rating
    if not rating:
        rating_range = {
            'gte': 0
        }
    else:
        rating_range = {
            'gte': rating
        }

    # paginations
    try:
        page_number = int(request.args.get('page', 1))
    except:
        page_number = 1

    if page_number == 1:
        from_range = 0
    elif page_number >= 2:
        from_range = (page_number - 1) * 20

    # Search only for main categories
    def main_c_s(query):
        main_category_search = {
            'query': {
                'bool': {
                    'must': [
                        {
                            'match': {
                                # match main categoryname
                                'categoryInfo.main.categoryName': {
                                    'query': query
                                }
                            }
                        }
                    ],
                    'filter': [
                        {    # If you set the price,
                            'range': {
                                'price': range
                            }
                        },
                        {    # If you set the rating,
                             # However, rating doesn't exist now
                             # So, Process with downloadCount
                            'range': {
                                'downloadCount': rating_range
                            }
                        }
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
        return main_category_search

    # Search main and middle category
    def middle_c_s(query):
        middle_category_search = {
            'query': {
                'bool': {
                    'must': [
                        {
                            'match': {
                                'categoryInfo.middle.categoryName': {
                                    'query': query
                                }
                            }
                        }
                    ],
                    'filter': [
                        {
                            'range': {
                                'price': range
                            }
                        },
                        {
                            'range': {
                                'downloadCount': rating_range
                            }
                        }
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
        return middle_category_search

    # Search main, middle and mood
    # However, mood field doesn't exist now
    # So, not use now
    def mood_c_s(query):
        mood_category_search = {
            'query': {
                'bool': {
                    'must': [
                        {
                            'match': {
                                'categoryInfo.main.categoryName.jaso': {
                                    'query': main_category_sea,
                                    'analyzer': 'suggest_search_analyzer',
                                    'operator': 'and'
                                }
                            },
                            'match': {
                                'categoryInfo.middle.categoryName.jaso': {
                                    'query': middle_category_sea,
                                    'analyzer': 'suggest_search_analyzer',
                                    'operator': 'and'
                                }
                            },
                        }
                    ],
                    'should': [
                        {
                            'match': {
                                'categoryInfo.main.categoryName.ngram': {
                                    'query': main_category_sea,
                                    'analyzer': 'my_ngram_analyzer',
                                    'operator': 'and'
                                }
                            },
                            'match': {
                                'categoryInfo.middle.categoryName.ngram': {
                                    'query': middle_category_sea,
                                    'analyzer': 'my_ngram_analyzer',
                                    'operator': 'and'
                                }
                            },
                        }
                    ],
                    'filter': [
                        {
                            'range': {
                                'price': range
                            },
                        },
                        {
                            'match': {
                                'tags.tagName': {
                                    'query': mood
                                }
                            }
                        }
                    ]
                }
            },
            'from': from_range,
            'size': 20
        }
        return mood_category_search

    res = {'_id': []}
    
    # Case main or main and middle
    if main or middle:
        if middle: # case main and middle
            if not mood: # not use now
                category_search_result = es.search(index='presentations', body=middle_c_s(middle))
            else:
                category_search_result = es.search(index='presentations', body=mood_c_s(i))

        else: # case main
            category_search_result = es.search(index='presentations', body=main_c_s(main))
        
        # final search result return.
        for i in category_search_result['hits']['hits']:
            if i['_id'] not in res['_id']: # 중복제거
                res['_id'].append(i['_id'])

    return jsonify(res)
```
