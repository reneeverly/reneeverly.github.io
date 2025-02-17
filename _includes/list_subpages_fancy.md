{% comment %}
{% raw %}
This sorts direct descendents out front, then creates headers for subfolders and displays those descendents.
{% endraw %}
{% endcomment %}

{% assign parent_url = page.url %}

{% assign pages = site.pages | sort: "url" | group_by_exp: "item", "item.dir" | where_exp: "collection", "collection.name contains page.dir" %}

{% for collection in pages %}
{% if page.dir != collection.name %}
### {{ collection.name }}
{% endif %}
{% for page in collection.items %}
{% if page.url != parent_url %}
* [{{ page.title }}]({{ page.url }})
{% endif %}
{% endfor %}
{% endfor %}

