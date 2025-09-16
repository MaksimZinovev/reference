# Hexo Documentation Snippets

## Translating Documentation

- [translating documentation](/docs/contributing.html#Translating)

## Hexo Site API Documentation

```apidoc
list_posts([options])
  Inserts a list of posts.
  Parameters:
    options: Object - Configuration options for listing posts.
      orderby: string - The criteria for sorting posts (e.g., 'date'). Default: 'date'.
      order: number | string - The sorting order. '1' or 'asc' for ascending, '-1' or 'desc' for descending. Default: 1.
      style: string - The display style for the post list. 'list' displays posts without order. Use false or any other value to disable. Default: 'list'.
      separator: string - Separator for tags (only works when style is not 'list'). Default: ','.
      class: string - Class name for the post list. Default: 'post'.
      amount: number - The number of posts to display. 0 means unlimited. Default: 6.
      transform: function - A function to change the display of the post name.

tagcloud([tags], [options])
  Inserts a tag cloud.
  Parameters:
    tags: Array - An array of tags (optional).
    options: Object - Configuration options for the tag cloud.
      min_font: number - Minimum font size. Default: 10.
      max_font: number - Maximum font size. Default: 20.
      unit: string - The unit for font size (e.g., 'px'). Default: 'px'.
      amount: number - The total number of tags to display. Default: unlimited.
      orderby: string - The criteria for sorting tags. Default: 'name'.
      order: number | string - The sorting order. '1' or 'asc' for ascending, '-1' or 'desc' for descending. Default: 1.
      color: boolean - Whether to apply colors to the tag cloud. Default: false.
      start_color: string - The starting color for the gradient (e.g., '#b700ff', 'rgba(183, 0, 255, 1)'). Works only when color is true.
      end_color: string - The ending color for the gradient (e.g., '#b700ff', 'rgba(183, 0, 255, 1)'). Works only when color is true.
      class: string - Class name prefix of tags.
      level: number - The number of different class names. Only works when 'class' is set. Default: 10.
      show_count: boolean - Whether to display the post count for each tag. Default: false.
      count_class: string - Function to change the display style of the tag name. Default: 'count'.
```

## Hexo Injector API

```apidoc
hexo.extend.injector.register(entry, value, to)

  entry <string>: Where the code will be injected inside the HTML.
    Support those values:
      - head_begin: Inject code snippet right after <head> (Default).
      - head_end: Inject code snippet right before </head>.
      - body_begin: Inject code snippet right after <body>.
      - body_end: Inject code snippet right before </body>.

  value <string> | <Function>: The code snippet to be injected. A function that returns string is supported.

  to <string>: Which page will code snippets being injected.
    - default: Inject to every page (Default).
    - home: Only inject to home page.
    - post: Only inject to post pages.
    - page: Only inject to pages.
    - archive: Only inject to archive pages.
    - category: Only inject to category pages.
    - tag: Only inject to tag pages.
    - Custom layout name could be used as well.
```

## List Tags API Documentation

```apidoc
list_tags(tags, options)

Inserts a list of tags with customizable options.

Parameters:
- tags: The collection of tags to display (e.g., site.tags).
- options: An object to configure the tag list display.
  - orderby: The field to order tags by. Default: 'name'.
  - order: The order direction. '1' or 'asc' for ascending, '-1' or 'desc' for descending. Default: 1.
  - show_count: Whether to display the count of posts for each tag. Default: true.
  - style: The display style for the tags. 'list' for an unordered list, false or other values to disable list styling. Default: 'list'.
  - separator: Separator between tags when not using 'list' style. Default: ','.
  - class: Class name for the tag list. Can be a string for a single class, or an object for advanced customization of different elements (ul, li, a, label, count).
    - class.ul: `<ul>` class name (only for style 'list'). Default: 'tag-list'.
    - class.li: `<li>` class name (only for style 'list'). Default: 'tag-list-item'.
    - class.a: `<a>` class name. Default: 'tag-list-link' (list style), 'tag-link' (normal style).
    - class.label: `<span>` class name for the tag label (only for normal style). Default: 'tag-label'.
    - class.count: `<span>` class name for the tag counter (only when show_count is true). Default: 'tag-list-count' (list style), 'tag-count' (normal style).
  - transform: A function to modify the display of tag names.
  - amount: The number of tags to display. 0 means unlimited. Default: 0.
  - suffix: A suffix to add to the tag link.

Examples:
<%- list_tags(site.tags, {class: 'classtest', style: false, separator: ' | '}) %>
<%- list_tags(site.tags, {class: 'classtest', style: 'list'}) %>
<%- list_tags(site.tags, {class: {ul: 'ululul', li: 'lilili', a: 'aaa', count: 'ccc'}, style: false, separator: ' | '}) %>
<%- list_tags(site.tags, {class: {ul: 'ululul', li: 'lilili', a: 'aaa', count: 'ccc'}, style: 'list'}) %>
```

## Serving Hexo Documentation Locally

```bash
hexo server
```

## Hexo Console API

```apidoc
hexo.extend.console.register(name, desc, options, callback)
  - Registers a console command.
  - Parameters:
    - name: (string) The name of the command.
    - desc: (string) A brief description of the command.
    - options: (object, optional) Configuration for command usage, arguments, and options.
      - usage: (string) Defines the command's usage pattern.
      - arguments: (Array<object>, optional) Describes command arguments.
        - name: (string) The argument name.
        - desc: (string) Description of the argument.
      - options: (Array<object>, optional) Describes command-line options.
        - name: (string) The option flag (e.g., '-o, --option').
        - desc: (string) Description of the option.
    - callback: (function) The function to execute when the command is run.
      - args: (object) Parsed arguments from the command line (using Minimist).
  - Example:
    hexo.extend.console.register("new", "Create a new post", function (args) {
      // command logic here
    });
```

## Setting Up Hexo Documentation Development

```bash
npm install hexo-cli -g # If you don't have hexo-cli installed
git clone https://github.com/<username>/site.git
cd site
npm install
```

## Site Variables Documentation

```apidoc
Site Variables:

page.month:
  Description: Archive month (2-digit without leading zeros)
  Type: number

page.category:
  Description: Category name
  Type: string

page.tag:
  Description: Tag name
  Type: string
```

## Show Version

```bash
$ hexo version
```

## Hexo Initialization Options

```apidoc
Hexo Instance Initialization Options:

  - `debug` (boolean): Enable debug mode. Displays debug messages and saves `debug.log`. Default: `false`
  - `safe` (boolean): Enable safe mode. Disables plugin loading. Default: `false`
  - `silent` (boolean): Enable silent mode. Suppresses terminal output. Default: `false`
  - `config` (string): Path to the configuration file. Default: `_config.yml`
  - `draft` / `drafts` (boolean): Enable inclusion of drafts in the posts list. Example: when using `hexo.locals.get('posts')`. Default: `render_drafts` from `_config.yml`
```

## Publishing a Plugin

- [Publish a plugin](/docs/plugins.html#Publishing)
