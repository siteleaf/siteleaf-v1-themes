Siteleaf Theme Documentation
============================

Getting started
---------------

Siteleaf uses the popular [Liquid](https://github.com/Shopify/liquid) syntax for themes.

There are two types of markup in Liquid:

1) Output markup (which may resolve to text) is surrounded by
```html
{{ matched pairs of curly brackets (ie, braces) }}
```

2) Tag markup (which cannot resolve to text) is surrounded by
```html
{% matched pairs of curly brackets and percent signs %}
```

If you are new to Liquid, a good place to start is [Liquid for Designers](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers).


Variables
=========

Site
----

`site` contains global variables available to all pages.

Get the title of your website:

```html
{{site.title}}
```

Loop through `site.pages` to build a menu:

```html
<nav>
  <ul>
  {% for page in site.pages %}
    <li><a href="{{page.url}}"{% if page.url == url %} class="selected"{% endif %}>{{page.title}}</a></li>
  {% endfor %}
  </ul>
</nav>
```

Content
-------

Get the title and body for the current page:

```html
<h2>{{title}}</h2>
{{body}}
```

Posts
-----

Loop through posts on the current page:

```html
{% for post in posts %}
<article>
  <h3><a href="{{post.url}}">{{post.title}}</a></h3>
  {{post.body}}
</article>
{% endfor %}
```

Metadata
--------

Get metadata for key `Color`:

```html
{{meta.color}}
```

Get metadata for key `My Color`:

```html
{{meta['My Color']}}
```

Loop through metadata:

```html
<dl>
{% for data in meta %}
  <dt>{{data.key}}</dt> <dd>{{data.value}}</dd>
{% endfor %}
</dl>
```

Taxonomy
--------

Get first tag in `Tags`:

```html
{{taxonomy.tags.first}}
```

Loop through `Tags`:

```html
<ul>
{% for tag in taxonomy.tags %}
  <li><a href="{{tag.url}}">{{tag.value}}</a></li>
{% endfor %}
</ul>
```

Loop through all taxonomy sets:

```html
<ul>
{% for tag_set in taxonomy %}
  <li>{{tag_set.key}} ({{tag_set | size}} tags)
    <ul>
    {% for tag in tag_set %}
      <li><a href="{{tag.url}}">{{tag.value}}</a></li>
    {% endfor %}
    </ul>
  </li>
{% endfor %}
</ul>
```


Naming your files
=================

Siteleaf themes may contain one or more templates. 

Your base template should always be called `default.html`. 

Additional templates can be applied to other pages by following the same URL structure:

```
URL                Template
---                --------
/                  index.html
/blog              blog.html (or blog/index.html)
/blog/new-post     blog/default.html
/blog/archive      blog/archive.html (or blog/archive/index.html)
```


Includes
--------

Includes (or partials) can be added by using the `include` tag:

```html
{% include 'header' %}
```

Include files should be prepended with an underscore. In the above example, Siteleaf will load `_header.html`.