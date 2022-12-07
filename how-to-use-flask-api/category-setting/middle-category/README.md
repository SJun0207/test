---
description: Returns middle category list
---

# Middle category

{% swagger method="post" path="/get_middle_categories" baseUrl="http://127.0.0.1:3000" summary="get_middle_categories" %}
{% swagger-description %}


![](../../../.gitbook/assets/2.png)


{% endswagger-description %}

{% swagger-parameter in="query" name="main" type="String" required="true" %}
ex) 음식
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```python
"POST /get_middle_categories?main=음식 HTTP/1.1" 200 -
```
{% endswagger-response %}
{% endswagger %}
