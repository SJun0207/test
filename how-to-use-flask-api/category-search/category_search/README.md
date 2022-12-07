---
description: Return documents based on selected criteria
---

# category\_search

{% swagger method="post" path="/category_search" baseUrl="http://127.0.0.1:3000" summary="category_search" expanded="false" %}
{% swagger-description %}
Request different parameters depending on the selected criteria

![](../../../.gitbook/assets/5.png)
{% endswagger-description %}

{% swagger-parameter in="query" name="main" type="String" %}
ex) 음식
{% endswagger-parameter %}

{% swagger-parameter in="query" name="middle" type="String" %}
ex) 한식
{% endswagger-parameter %}

{% swagger-parameter in="query" name="price" type="Int" %}
ex) 3000 | 3000, 10000
{% endswagger-parameter %}

{% swagger-parameter in="query" name="선호도" type="Int" %}
ex) 5
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```python
"POST /category_search?main=음식&middle=패스트푸드&rating=1&price=3000,10000 HTTP/1.1" 200 -

"POST /category_search?main=음식&middle=패스트푸드 HTTP/1.1" 200 -

"POST /category_search?main=음식&middle=패스트푸드&rating=1 HTTP/1.1" 200 -
```
{% endswagger-response %}
{% endswagger %}
