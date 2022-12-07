---
description: Elasticsearch source code description
---

# Source Code

```python
# How to run a flask. API name gen_middle_categories
list_appear.route('/get_middle_categories', methods=['POST'])
def get_middle_categories():
    # main is essential.
    main_category = request.args.get('main', False)

    middle_category_body = {
        'query': {
            'bool': {
                'must': [
                    {
                        'match': {
                            # Return only those with a category of level 1
                            'categoryLevel': 1
                        },
                        'match': {
                            # Return only those with same as the main value entered
                            'parentCategory.categoryName': main_category
                        }
                    },
                ]
            }
        },
        'size': 10000
    }

    res = {'middle category list': []}
    if main_category:
        # Search in categories index.
        middle_search_result = es.search(index='categories', body=middle_category_body)
        
        # final search result return.
        for i in middle_search_result['hits']['hits']:
            res['middle category list'].append(i['_source']['categoryName'])

    return jsonify(res)
```
