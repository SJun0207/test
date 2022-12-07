---
description: Elasticsearch source code description
---

# Source Code

```python
# How to run a flask. API name price_search
@pricesearch.route('/price_search', methods=['POST'])
def price_search():
    # http://daplus.net/python-%ED%94%8C%EB%9D%BC%EC%8A%A4%ED%81%AC-%EC%9A%94%EC%B2%AD%EC%97%90%EC%84%9C-%EC%88%98%EC%8B%A0-%ED%95%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8-%EC%98%A4%EA%B8%B0/
    # Need to change to match request type.
    price_range = request.args.get('price', False)
    rating = request.args.get('rating', False)

    # paginations
    try:
        page_number = int(request.args.get('page', 1))
    except:
        page_number = 1

    if page_number == 1:
        from_range = 0
    elif page_number >= 2:
        from_range = (page_number - 1) * 20

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

    body = {
        'query': {
            'bool': {
                'must': [
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
                ]
            }
        },
        'sort': {
            'price': 'asc'
        },
        'from': from_range,
        'size': 20
    }

    res = {'_id': []}

    search_result = es.search(index='presentations', body=body)
    
    # final search result return.
    for i in search_result['hits']['hits']:
        if i['_id'] not in res['_id']:
            res['_id'].append(i['_id'])

    return jsonify(res)

```
