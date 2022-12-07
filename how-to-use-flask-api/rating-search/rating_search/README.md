---
description: Return documents based on selected criteria
---

# rating\_search

{% swagger method="post" path="/rating_search" baseUrl="http://127.0.0.1:3000" summary="rating_search" %}
{% swagger-description %}


![](../../../.gitbook/assets/7.png)


{% endswagger-description %}

{% swagger-parameter in="query" name="rating" type="Int" %}
ex) 5
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```python
"POST /rating_search?rating=50 HTTP/1.1" 200 -
```
{% endswagger-response %}
{% endswagger %}
