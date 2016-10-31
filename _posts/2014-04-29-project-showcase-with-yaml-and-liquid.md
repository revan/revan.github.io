---
layout: post
title: "Project Showcase with YAML and Liquid"
---

When I remade my website with [Jekyll](http://jekyllrb.com/) last week, I made a project showcase page (check it out in the sidebar!).
In under twenty lines of code, I built a system for programmatically display a list of projects with descriptions, and look nice doing it.

## YAML
Jekyll uses [YAML](http://www.yaml.org/), a human readable data format like JSON, for configuration.
My system reads the data to display from a file `projects.yml`, stored in `_data/`.

The syntax is pretty self explanatory:

```yaml
- name: "Project 1"
  url: https://example.com
  description: "A short description"
  image: https://example.com/optional.png

- name: "Project 2"
  url: https://example.com
  description: "
  Descriptions can span
  several lines and
  include <b>HTML</b>
  "
  prize: "Best Example of YAML"
```

## Liquid
Jekyll also uses [Liquid Templating](http://liquidmarkup.org/) for compile-time HTML generation, allowing for some pretty powerful stuff.
I use [GitHub Pages](https://pages.github.com/) for hosting, which means on every push, the project page (and everything, really) is regenerated.

{% raw %}
Liquid Templates provides conditional logic through the `{%  %}` tags, and text-resolving markup with the `{{  }}` tags.

The relevant source for the page in question:

```html
{% for project in site.data.projects %}
	<div class="box css3-shadow">
		{% if project.image %}
			<a href="{{project.url}}">
				<img class="image-rounded" src="{{project.image}}">
			</a>
		{% endif %}
		<h3><a href="{{project.url}}">{{project.name}}</a></h3>
		{{project.description}}
		{% if project.prize %}
			<br><i class="fa fa-trophy fa-lg"></i> {{project.prize}}
		{% endif %}
	</div>
{% endfor %}
```
For each entry in the YAML file, I make a div (styled with the first CSS I found -- I'm not great at CSS). If we defined an image, insert that image, linking to the URL. A title linking to the URL and the description follow. If a prize was defined, display a [Font-Awesome](http://fortawesome.github.io/Font-Awesome/) trophy icon followed by the text.
{% endraw %}
