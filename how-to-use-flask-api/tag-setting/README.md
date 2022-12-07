---
description: This API returns a list of Tag
---

# Tag Setting

{% swagger method="post" path="/get_tags" baseUrl="http://127.0.0.1:3000" summary="get_tags" %}
{% swagger-description %}


![](../../.gitbook/assets/4.png)


{% endswagger-description %}

{% swagger-parameter in="query" name="main" type="String" required="true" %}
ex) 음식
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```python
"POST /get_tags?main=음식 HTTP/1.1" 200 -
```
{% endswagger-response %}
{% endswagger %}
