> #Deprecation notice
This plugin is no longer maintained and may not work with Pico 1.0.

>You should try this plugin: [Pico-Tags](https://github.com/PontusHorn/Pico-Tags)

picotags
========

Adds page tagging functionality to [Pico](http://pico.dev7studios.com/).
Based on [Pico Tags by Szymon Kaliski](https://github.com/szymonkaliski/Pico-Tags-Plugin), but only uses the provided hooks
and leaves the Pico core alone.

It gives you access to:
* The current page `meta.tags` array
* Each `page.tags` in the `pages` array
* The `tag_list` array of all used tags
* If on a `/tag/` URL, the `current_tag` variable

##Installation

Place `picotags.php` file into the `plugins` directory.

##Usage

Add a 'Tags' attribute into the page meta:

```
/*
Title: My First Blog Post
Description: It's a blog post about javascript and php
Author: Dan Reeves
Robots: index,follow

Date: 2013/10/02
Tags: js,javascript,php
*/
```

You can now access both the current page `meta.tags` and each `page.tags` in the `pagetags` array:
```html
{% if is_front_page %}
<!-- front page -->
    {% for page in pages %}
        {% if page.date %}
            <article>
                <h2><a href="{{ page.url }}">{{ page.title }}</a></h2>
                <p class="meta">Tags:
                    {% for tag in page.tags %}
                        <a href="{{ base_url }}/tag/{{ tag }}">#{{ tag }}</a>
                    {% endfor %}
                </p>
                {{ page.excerpt }}
            </article>
        {% endif %}
    {% endfor %}
<!-- front page -->
{% elseif meta.tags %}
<!-- blog post -->
    <article>
        <h2>{{ meta.title }}</h2>
        <p class="meta">Tags:
            {% for tag in meta.tags %}
                <a href="{{ base_url }}/tag/{{ tag }}">#{{ tag }}</a>
            {% endfor %}
        </p>
        {{ content }}
    </article>
<!-- blog post -->

{% elseif pages and meta.title != 'Error 404' %}
<!-- tags page -->
    All tags:
    <ul class="tags">
        {% for tag in tag_list %}
        <li><a href="/tag/{{ tag }}">#{{ tag }}</a></li>
        {% endfor %}
    </ul>
    <p>Posts tagged <a href="{{ page.url }}">#{{ current_tag }}</a>:</p>
    {% for page in pagetags %}
        {% if page.date %}
            <article>
                <h2><a href="{{ page.url }}">{{ page.title }}</a></h2>
                <p class="meta">Posted on {{ page.date_formatted }} by {{ page.author }}
                    <span class="tags"><br />Tags:
                        {% for tag in page.tags %}
                                <a href="{{ base_url }}/tag/{{ tag }}">#{{ tag }}</a>
                        {% endfor %}
                    </span>
                </p>
                {{ page.content }}
            </article>
        {% endif %}
    {% endfor %}
<!-- tags page -->
{% else %}
<!-- single page -->
<article>
    <h2>{{ meta.title }}</h2>
    {{ content }}
</article>
<!-- single page -->
{% endif %}
```

If you encounter any trouble with this template structure, you might want to try this one:
```
{% if is_front_page %}

    FRONT PAGE

{% elseif current_page is not empty %}

    {% if meta.tags is not empty %}

        <!-- BLOG POSTS WITH TAGS -->
        <article>
            <h2>{{ meta.title }}</h2>
            <p class="meta">Tags:
                {% for tag in meta.tags %}
                    <a href="{{ base_url }}/tag/{{ tag }}">#{{ tag }}</a>
                {% endfor %}
            </p>
            {{ content }}
        </article>

    {% else %}

        <!-- BLOG POSTS WITHOUT TAGS -->
        <article>
            <h2>{{ meta.title }}</h2>
            {{ content }}
        </article>

    {% endif %}

{% elseif current_page is empty %}

    {% if meta.title != 'Error 404' %}

        <!-- TAG PAGES : list of blog posts tagged with #... -->

        All tags:
        <ul class="tags">
            {% for tag in tag_list %}
            <li><a href="/tag/{{ tag }}">#{{ tag }}</a></li>
            {% endfor %}
        </ul>
        <p>Posts tagged <a href="{{ page.url }}">#{{ current_tag }}</a>:</p>
        {% for page in pagetags %}
            {% if page.date %}
                <article>
                    <h2><a href="{{ page.url }}">{{ page.title }}</a></h2>
                    <p class="meta">Posted on {{ page.date_formatted }} by {{ page.author }}
                        <span class="tags"><br />Tags:
                            {% for tag in page.tags %}
                                    <a href="{{ base_url }}/tag/{{ tag }}">#{{ tag }}</a>
                            {% endfor %}
                        </span>
                    </p>
                    {{ page.content }}
                </article>
            {% endif %}
        {% endfor %}

    {% endif %}
{% endif %}
```

### Personal template for tag pages

You can choose to use a personal template for tag pages instead of using conditions in your theme `index.html`.

First, you have to add a setting to your `config.php`:
```
$config['ptags_template'] = 'YOUR_TAG_TEMPLATE';
```
Then, you have to create a "YOUR_TAG_TEMPLATE.html" file inside your theme folder ; in this template page, you can simply add in the \<body\> section something like that:
```
<h1>Posts tagged <a href="{{ page.url }}">#{{ current_tag }}</a></h1>
{% for page in pagetags %}          
    <article>
        <h2><a href="{{ page.url }}">{{ page.title }}</a></h2>
        <p class="meta">
            <span class="tags"><br />Tags :
                {% for tag in page.tags %}
                    <a href="{{ base_url }}/tag/{{ tag }}">#{{ tag }}</a>
                {% endfor %}
            </span>
        </p>
        {{ page.excerpt }}
    </article>
{% endfor %}
<h1>All tags :</h1>
    <ul>
        {% for tag in tag_list %}
            <li><a href="/tag/{{ tag }}">#{{ tag }}</a></li>
        {% endfor %}
    </ul>
```

## Alphabetically sorted list

In your config.php :
```
$config['ptags_sort'] = true;
```

## Multicolumn output

For a two columns output :
- in your config.php :
```
$config['ptags_nbcol'] = 2;
```
- in your template output : 
```
<ul>
    {% for tag in tag_list_0 %}
        <li><a href="/tag/{{ tag }}">#{{ tag }}</a></li>
    {% endfor %}
</ul>
<ul>
    {% for tag in tag_list_1 %}
        <li><a href="/tag/{{ tag }}">#{{ tag }}</a></li>
    {% endfor %}
</ul>
```
## Adding meta keywords to \<head\>
```
{% if meta.tags %}
    <meta name="keywords" content="{{ meta.tags | join(',') }}">
{% endif %}
```

## Removing from the tags list the ones that are used in only one page
In your config.php :
```
$config['ptags_delunique'] = true;
```

## Excluding pages from the tags list

In your config.php, you can use the setting `ptags_exclude` and specify the pages you want to exclude from the tag_list through an array in which you can associate multiple values for a meta type :
```
$config['ptags_exclude'] = array(
    'template' => 'category',
    'title' => 'GCweb, qu\'est-ce que c\'est ?|CV',
    'category' => 'Maîtrise Sciences du langage|Master recherche Sciences du langage|Master professionnel Édition'
);
```

__WARNING__
- you have to use the `pipe (|)` as a separator;
- you have to escape single quotes with a backslash;
- be careful, in some case you can obtain 404 error when there is no articles to display in a tag page.
