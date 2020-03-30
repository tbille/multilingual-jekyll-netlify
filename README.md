# Multilingual Netlify CMS + Jekyll

[![Netlify Status](https://api.netlify.com/api/v1/badges/45aee436-08d0-4bec-9ce2-ac9b5e8542ea/deploy-status)](https://app.netlify.com/sites/youthful-stonebraker-b0e09f/deploys)

This is a simple example of an implementation of a multilingual site using [Jekyll](https://jekyllrb.com/) + [netlify-cms](https://www.netlifycms.org/).

![Screenshot from 2020-03-30 23-01-07](https://user-images.githubusercontent.com/2707508/77969017-9bc76c00-72e0-11ea-9bb5-9a67fc2b590e.png)


## How to run locally

**Optional:** In a terminal (to run the cms locally):
 - `cd <PROJECT>`
 - `PORT=8076 npx netlify-cms-proxy-server`

In another terminal:
 - `cd <PROJECT>`
 - `bundle exec jekyll serve`
 - You can now access the site: http://localhost:4000

## How does it works?

This explanation is based on the idea that the reader has knowledge of both techonologies.

Netlify-cms gets linked to a collection languages that will define the laguages available (here limited by [this issue](https://github.com/netlify/netlify-cms/issues/800)): 

``` yaml
  - label: "Languages"
    name: "languages"
    folder: "_data/languages"
    extension: "yml"
    format: "yml"
    create: false
    slug: "{{code}}"
    editor:
      preview: false
    fields:
      - { label: "Language", name: "language" , widget: string}
      - { label: "Country Code (for url)", name: "code" , widget: string}
```

This collection is located in the directory `_data/laguages` to allow the template to generate automatically the [list of available languages](https://github.com/tbille/multilingual-jekyll-netlify/blob/master/_includes/languages.html).

All the templates are located in the directory `_includes` in this example only `index.html` and `about.html`.

For every language there is a directory named by the code of the language: for example `fr`. This directory will contain all the layouts and include the corresponding template with the `lang` variable set as the code of the language. The [index in french](https://github.com/tbille/multilingual-jekyll-netlify/blob/master/fr/index.html) would look like this:

```
---
layout: default
---

{% include index.html lang="fr" %}
```

Once all this is setup let's have a look at the collections defined in [netlify-cms configuration file](https://github.com/tbille/multilingual-jekyll-netlify/blob/master/admin/config.yml#L24).

``` yaml
  - label: "Post"
    name: "post"
    folder: "_posts"
    slug: "{{year}}-{{month}}-{{day}}-{{title}}"
    create: true
    editor:
      preview: false
    fields:
      - label: Language
        name: language
        widget: relation
        collection: languages
        searchFields: ["language"]
        valueField: code
        displayFields: ["language"]
        default: "en"
      - { label: "Title", name: "title", widget: "string" }
      - { label: "Body", name: "body", widget: "markdown" }
```

Every collection have the field language which is a relation based on the list of languages. This allows the user to set a language for the content being written.



Every content created will have the variable `language` set in its frontmatter. This allows the templates to then [filter the relevant content](https://github.com/tbille/multilingual-jekyll-netlify/blob/master/_includes/index.html#L5) based on this variable.

*Et voilà !*

## Add a new language?

To add a new language, Italian for example:
- `touch _includes/languages/it.yaml`
- `$EDITOR _includes/languages/it.yaml`
- Insert this content:
```
language: Italiano
code: it
```
- `mkdir it`
- Add same the files that French (`fr/`) and change the variable `lang` to have the value `it`

## Change default language?

English has been set as default language. For that in each file contained in the language directory the line `permalink: /<CHANGE URL>` needs to be added. This line can be removed from the previous default language.

## What next?

- [ ] Script add language
- [ ] Create a GitHub action to automatically add the directory with the templates when a new language is added from the cms
- [ ] Handle URL for posts (is it really useful??)
- [ ] Swaggify a bit the templates

With <3 from toto
