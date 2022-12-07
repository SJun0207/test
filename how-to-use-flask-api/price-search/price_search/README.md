---
description: Return documents based on selected criteria
---

# price\_search

{% swagger method="post" path="/price_search" baseUrl="http://127.0.0.1:3000" summary="price_search" %}
{% swagger-description %}


![](../../../.gitbook/assets/6.png)


{% endswagger-description %}

{% swagger-parameter in="query" name="price" type="Int" %}
ex) 3000 | 3000,10000
{% endswagger-parameter %}

{% swagger-parameter in="query" name="rating" type="Int" %}
ex) 5
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```python
"POST /price_search?price=3000,10000 HTTP/1.1" 200 -

"POST /price_search?price=3000,10000&rating=5 HTTP/1.1" 200 -
```
{% endswagger-response %}
{% endswagger %}
