---
description: Elasticsearch source code description
---

# Source Code

```python
# How to run a flask. API name gen_main_categories
@list_appear.route('/get_main_categories', methods=['POST'])
def get_main_categories():
    main_category_body = {
        'query': {
            'bool': {
                'must': [
                    {
                        'match': {
                            # Return only those with a category of level 0
                            'categoryLevel': 0
                        },
                    }
                ]
            }
        },
        'size': 10000
    }

    res = {'main category list': []}
    # Search in categories index.
    main_search_result = es.search(index='categories', body=main_category_body)
    
    # final search result return.
    for i in main_search_result['hits']['hits']:
        res['main category list'].append(i['_source']['categoryName'])

    return jsonify(res)
```
