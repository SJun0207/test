---
description: Return documents containing keywords entered
---

# Full Search

{% swagger method="post" path="/total_search" baseUrl="http://127.0.0.1:3000" summary="total_search" %}
{% swagger-description %}


![](../../.gitbook/assets/1.png)


{% endswagger-description %}

{% swagger-parameter in="query" name="keyword" type="String" required="true" %}
ex) 음식
{% endswagger-parameter %}

{% swagger-parameter in="query" name="page" type="Int" required="true" %}
ex) 2
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```python
{
    "/total_search?keyword=음식&page=1 HTTP/1.1" 200 -
}
```
{% endswagger-response %}
{% endswagger %}
