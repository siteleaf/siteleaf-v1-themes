Siteleaf Theme Documentation
============================

- [Getting started](#getting-started)
- [Filters and Tags](#filters-and-tags)
- [Variables](#variables)
  - [Site](#site)
  - [Content (page, post, archive, taxonomy)](#content)
  - [Posts](#posts)
  - [Subpages](#subpages)
  - [Assets](#assets)
  - [Metadata](#metadata)
  - [Taxonomy and Tags](#taxonomy-and-tags)
- [Naming your files](#naming-your-files)
  - [Includes](#includes)
- [Sample themes](#sample-themes)
- [Testing sites locally](#testing-sites-locally)
- [Further reading](#further-reading)
- [Other resources](#other-resources)
- [Contributing](#contributing)


Getting started
---------------

Siteleaf uses the popular [Liquid](https://github.com/Shopify/liquid) syntax for themes. If you can write HTML, you’ll have no problem using Liquid.

Here’s how a simple template might look:

```html
<html>
<head>
	<title>{{site.title}} | {{title}}</title>
</head>
<body>
	
  <h1>{{title}}</h1>
  
  <article>{{body}}</article>
  
  {% if parent %}
  <p><a href="{{parent.url}}">&larr; Go back</a></p>
  {% endif %}

</body>
</html>
```

As you can see, there are two types of markup in Liquid:

1) Output markup (which may resolve to text) is surrounded by
```html
{{ matched pairs of curly brackets (ie, braces) }}
```

2) Tag markup (which cannot resolve to text) is surrounded by
```html
{% matched pairs of curly brackets and percent signs %}
```

If you are new to the Liquid syntax, a good place to start is [Liquid for Designers](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers).


Filters and Tags
----------------

Siteleaf supports all [Standard Liquid Filters](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers#standard-filters) and [Liquid Tags](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers#tags). 

Siteleaf is also compatible with [Jekyll Liquid Filters](http://jekyllrb.com/docs/templates/).

In addition, Siteleaf adds the following custom filters:

Filter             | Description                                                    | Example
--------           | -----------                                                    | -------
`fallback`         | Provide a default value if variable is blank.                  | {{title &#124; fallback:"Untitled"}}
`json`		   | Formats content as [JSON (JavaScript Object Notation)](http://en.wikipedia.org/wiki/JSON)  | {{site.posts &#124; json}}
`markdown`         | Converts [Markdown](http://daringfireball.net/projects/markdown/) to HTML, use `true` to apply Smartypants.  | {{title &#124; markdown:true}}
`smartypants`      | Applies [Smartypants](http://daringfireball.net/projects/smartypants/) (smart quotes, em/en dashes, etc).  | {{title &#124; smartypants}}
`slug`		   | Converts a string to its URL-friendly, slug form.			    | {{meta.custom_field &#124; slug}}


Variables
=========

Site
----

`site` contains global variables available to all pages.

Variable           | Description
--------           | -----------
`site.title`       | The title of your website.
`site.domain`      | Your website’s domain name (ie. `barlawrence.com`).
`site.permalink`   | Your website’s full address (ie. `http://barlawrence.com`).
`site.pages`       | A nested array of pages.
`site.posts`       | Array of all posts in all pages.
`site.feed_url`    | URL to your website’s [RSS](http://en.wikipedia.org/wiki/RSS) feed (ie. `http://barlawrence.com/feed.xml`).
`site.sitemap_url` | URL to your website’s [Sitemap](http://www.sitemaps.org) file (ie. `http://barlawrence.com/sitemap.xml`).
`site.date`        | Date of most recent publish.

For example, use the following Liquid to get the title of your website:

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

Variable           | Description
--------           | -----------
`type`             | Can be `page`, `post`, `archive`, `taxonomy`, or `tag`.
`title`            | Title of content.
`url`              | URL to object without domain (ie. `/blog/my-post`).
`permalink`        | Full URL to object with domain (ie. `http://mysite.com/blog/my-post`).
`body`             | Body in HTML (rendered from Markdown), available in `page` and `post` types.
`body_raw`         | Body in raw Markdown, available in `page` and `post` types.
`excerpt`          | Shortened version of the body (use 2 empty lines to break), available in `page` and `post` types.
`excerpt_raw`      | Excerpt in raw Markdown, available in `page` and `post` types.
`assets`           | Array of [assets](#assets), available on `page` and `post` types.
`meta`             | Array of [metadata](#metadata), available on `page` and `post` types.
`taxonomy`         | Array of [taxonomy](#taxonomy), available on `post` type only.
`tags`             | Array of [tags](#tags), available on `taxonomy` type only.
`pages`            | Array of child-pages, available on `page` type only.
`posts`            | Array of posts, available in `page`, `archive`, and `tag` types.
`previous`         | The previous `page` or `post`, available in `page` or `post` types.
`next`             | The next `page` or `post`, available in `page` or `post` types.
`current`          | Alias of the curent `page` or `post`, available in `page` or `post` types.
`parent`           | Parent page object (if exists).
`archive_url`      | URL to archive page, available on `page` type only.
`date`             | Date of publish, available in `page` and `post` types.
`author.fullname`  | Full name of author, available in `page` and `post` types.
`author.firstname` | First name of author, available in `page` and `post` types.
`author.lastname`  | Last name of author, available in `page` and `post` types.
`author.email`     | Author’s email, available in `page` and `post` types.
`author.avatar`    | Author’s Gravatar URL, available in `page` and `post` types.

Get the title and body for the current page:

```html
<h2>{{title}}</h2>

{{body}}
```

Check for parent page:

```html
{% if parent %}
  <a href="{{parent.url}}">&larr; Back to {{parent.title}}</a>
{% endif %}
```


Posts
-----

See [Content](#content) for available variables.

Count the number of posts:

```html
This page has {{posts | size}} posts.
```

Loop through the first 20 posts on the current page:

```html
{% for post in posts limit:20 %}
<article>
  <header><a href="{{post.url}}">{{post.title}}</a></header>
  {{post.body}}
  <footer>Posted on {{post.date | date: "%b %d, %Y"}} by {{post.author.fullname}}</footer>
</article>
{% endfor %}
```


Subpages
-----

See [Content](#content) for available variables.

Count the number of subpages:

```html
This page has {{pages | size}} subpages.
```

Loop through the subpages:

```html
{% for page in pages %}
<article>
  <h3><a href="{{page.url}}">{{page.title}}</a></h3>
  {{page.body}}
</article>
{% endfor %}
```


Assets
------

Variable           | Description
--------           | -----------
`type`             | Can be `image`, `audio`, `video`, or `other`.
`filename`         | Name of file (ie. `photo.jpg`).
`url`              | URL to object without domain (ie. `/assets/photo.jpg`).
`permalink`        | Full URL to object with domain (ie. `http://mysite.com/assets/photo.jpg`).
`content_type`     | MIME type of asset (ie. `image/jpeg`).
`filesize`         | Size of file in bytes (ie. `1024`).
`date`             | Date asset was created.

Count the number of assets:

```html
{{assets | size}}
```

Loop through assets:

```html
{% for asset in assets %}
  {% if asset.type == 'image' %}
  <img src="{{asset.url}}">
  {% elsif asset.type == 'audio' %}
  <audio><source src="{{asset.url}}" type="{{asset.content_type}}"></audio>
  {% elsif asset.type == 'video' %}
  <video><source src="{{asset.url}}" type="{{asset.content_type}}"></video>
  {% elsif asset.type == 'other' %}
  <a href="{{asset.url}}">Download {{asset.filename}}</a></li>
  {% endif %}
{% endfor %}
```


Metadata
--------

Variable           | Description
--------           | -----------
`meta`             | Array of all metadata key/value pairs.
`meta.KEY`         | Get single metadata by key (ie. `Color`).

Available for each metadata:

Variable           | Description
--------           | -----------
`key`              | Name of metadata key (ie. `Color`).
`value`            | Value of metadata (ie. `Red`).

Count number of metadata fields:

```html
{{meta | size}}
```

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

Taxonomy and Tags
-----------------

Siteleaf allows you to use multiple tag sets called Taxonomy. By default, each site will have one set called `Tags`.

Variable           | Description
--------           | -----------
`taxonomy`         | Array of all taxonomy sets.
`taxonomy.KEY`     | Get tags by set name, ie. `Tags`.

### Taxonomy:

Variable           | Description
--------           | -----------
`key`              | Name of taxonomy set, ie. `Tags`.
`slug`             | URI slug for set, ie. `tags`.
`url`              | URL for set page without domain, ie. `/blog/tags`
`permalink`        | URL for set page with domain, ie. `http://mysite.com/blog/tags`
`tags`             | Array of [tags](#tags) in set.
`tags.KEY`         | Get single tag by name, ie. `Design`.

### Tags:

Variable           | Description
--------           | -----------
`value`            | Name of tag, ie. `Design`.
`slug`             | URI slug for tag set, ie. `design`.
`url`              | URL for tag page without domain, ie. `/blog/tags/design`
`permalink`        | URL for tag page with domain, ie. `http://mysite.com/blog/tags/design`
`posts`            | Array of [posts](#posts) with this tag.

Count number of tags in the default `Tags` set:

```html
{{taxonomy['tags'] | size}}
```

Count number of tags in the `Colors` tag set:

```html
{{taxonomy['colors'] | size}}
```

Get first tag in the `Colors` tag set:

```html
{{taxonomy['colors'].first}}
```

Loop through the `Tags` set:

```html
<ul>
{% for tag in taxonomy['tags'] %}
  <li><a href="{{tag.url}}">{{tag.value}}</a></li>
{% endfor %}
</ul>
```

Loop through the `Colors` set:

```html
<ul>
{% for tag in taxonomy['colors'] %}
  <li><a href="{{tag.url}}">{{tag.value}}</a></li>
{% endfor %}
</ul>
```

Loop through all tag sets:

```html
<ul>
{% for set in taxonomy %}
  <li>{{set.key}} ({{set | size}} tags)
    <ul>
    {% for tag in set %}
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

Additional templates can be applied to other pages by following the same URL structure as your website:

URL               | Template
---               | --------
*                 | default.html (required, used by default)
/                 | index.html
/blog             | blog.html (or blog/index.html)
/blog/new-post    | blog/default.html
/blog/archive     | blog/archive.html (or blog/archive/index.html)


Includes
--------

Includes (or partials) can be added by using the `include` tag:

```html
{% include 'header' %}

{% include 'includes/menu' %}
```

Include files should be prepended with an underscore. In the above examples, Siteleaf will look for `_header.html` and `_includes/menu.html` respectively.


Sample themes
=============

This repo contains the default Siteleaf theme as well as a basic barebones template to help you get started.

More sample themes can be found in the wiki:

https://github.com/siteleaf/siteleaf-themes/wiki/Siteleaf-themes-on-Github

If you have a theme you'd like to share with the community, feel free to add it.


Testing sites locally
=============

To test and build themes locally, [install the Siteleaf Gem](https://github.com/siteleaf/siteleaf-gem)

Further reading
=============
- [Siteleaf overview](http://www.siteleaf.com/blog/overview/) - An overview of the top Siteleaf features
- [Developing sites and themes](http://www.siteleaf.com/blog/developing-sites/) - Video walkthrough of creating Siteleaf themes and developing locally
- [An Introduction to Siteleaf](http://destroytoday.com/blog/an-introduction-to-siteleaf)
- [Hello World, I'm Siteleaf](http://destroytoday.com/blog/hello-world-im-siteleaf)
- [Metadata in Siteleaf](http://destroytoday.com/blog/metadata-in-siteleaf/) 
- [Taxonomy in Siteleaf](http://destroytoday.com/blog/taxonomy-in-siteleaf/)
- [Markdown in Siteleaf](http://destroytoday.com/blog/markdown-in-siteleaf/)
- [Porting a theme to Siteleaf](http://destroytoday.com/blog/porting-a-theme-to-siteleaf)
- [Layouts in Siteleaf](http://destroytoday.com/blog/layouts-in-siteleaf)


Other resources
=============

- [Liquid for Designers](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers)
- [Liquid syntax highlighting for Sublime Text 2](https://bitbucket.org/granteagon/shopify-liquid/src/ccb7a8040615/README.rst)
- [Liquid Wiki](http://wiki.shopify.com/Liquid)


Contributing
============

Help us improve this documentation:

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
