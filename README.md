picotags
========

Adds page tagging functionality to [Pico](http://pico.dev7studios.com/).
Based on [Pico Tags by Szymon Kaliski](https://github.com/szymonkaliski/Pico-Tags-Plugin), but only uses the provided hooks
and leaves the Pico core alone.

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

You can now access both the current page `meta.tags` and each `page.tags` in the `pages` array:
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
{% endif %}
```
##Demo
Coming soon
