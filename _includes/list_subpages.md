{% comment %}
{% raw %}
This displays direct descendants and index pages from subfolders.

Assumes that this is being run on an index.md/.html page - which has url of `...folder` and not `...folder/index.html`
This is why minus 1 for parent index, and minus 2 for child index
{% endraw %}
{% endcomment %}

{% assign pageurly_array = page.url | split: "/" %}
{% assign parent_index = pageurly_array.size | minus: 1 %}
{% assign parent = pageurly_array[parent_index] %}

{% assign pages = site.pages | sort: "url" %}
{% for page in pages %}
{%- assign pageurl_array = page.url | split: "/" -%}
{%- assign index = pageurl_array.size | minus: 2 -%}
{%- if pageurl_array[index] == parent -%}
* [{{ page.title }}]({{ page.url }})
{%- endif %}
{% endfor %}
