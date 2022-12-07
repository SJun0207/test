---
description: Elasticsearch source code description
---

# Source Code

```python
# How to run a flask. API name gen_tags
@list_appear.route('/get_tags', methods=['POST'])
def get_tags():
    # main is essential.
    main_category = request.args.get('main', False)

    tag_search = {
        'query': {
            'bool': {
                'must': [
                    {
                        'match': {
                            # Return only those with same as the main value entered
                            'elderCategoryInfo.categoryName': {
                                'query': main_category,
                            }
                        },
                    }
                ]
            }
        },
        'size': 10000
    }

    res = {'tags': []}

    if main_category:
        # Search in hashtags index.
        tag_search_result = es.search(index='hashtags', body=tag_search)
        
        # final search result return.
        for i in tag_search_result['hits']['hits']:
            res['tags'].append(i['_source']['tagName'])

    return jsonify(res)
```
