# Stylemark &nbsp; ![npm (scoped)](https://img.shields.io/npm/v/@nansen/stylemark?style=flat-square)

> **NOTICE: This is a fork of [Stylemark](https://github.com/mpetrovich/stylemark/).** The [original author](https://github.com/mpetrovich/) abandoned it. We will from time to time add more functionality and changes that suits our specific use cases.

Generate interactive style guides from Markdown. Document your style guide components in code comments or Markdown files, and Stylemark will generate a static HTML site with live, interactive components.

![Bootstrap style guide](https://user-images.githubusercontent.com/1235062/31162551-2d8f6da6-a8ac-11e7-8874-9e8a2c1c6680.png)

### Examples

-   [Bootstrap](http://stylemark-bootstrap.surge.sh/)
-   [React](http://stylemark-react.surge.sh/)
-   [Ember](http://stylemark-ember.surge.sh/)

## Installation

Requires Node 6.x+

```sh
npm install -g stylemark
```

For a native app with built-in auto-updating/hot-reloading, see [Stylemark App](https://github.com/nextbigsoundinc/stylemark-app).

## Documenting style guide components

Documenting style guide components is as easy as writing Markdown. Components can be documented in dedicated Markdown files or as comment blocks within any source code. [**See the full Stylemark spec**](README-SPEC.md).

### As a dedicated Markdown file

````markdown
---
name: Button
category: Components
---

Buttons can be used with `<a>`, `<button>`, and `<input>` elements.

Types of buttons:

-   Default: Standard button
-   Primary: Provides extra visual weight and identifies the primary action in a set of buttons
-   Success: Indicates a successful or positive action

```types.html
<button class="btn btn-default">Default</button>
<button class="btn btn-primary">Primary</button>
<button class="btn btn-success">Success</button>
```
````

### As a comment block within source code

The language of your source code doesn't matter as long as the docs are in `/* … */` comments.

````css
/*
---
name: Button
category: Components
---

Buttons can be used with `<a>`, `<button>`, and `<input>` elements.

Types of buttons:
- Default: Standard button
- Primary: Provides extra visual weight and identifies the primary action in a set of buttons
- Success: Indicates a successful or positive action

```types.html
<button class="btn btn-default">Default</button>
<button class="btn btn-primary">Primary</button>
<button class="btn btn-success">Success</button>
```
*/
.btn {
    display: inline-block;
    text-align: center;
    vertical-align: middle;
    …
}
.btn-default {
    …
}
````

## Generating the HTML style guide

### In Node.js

```js
stylemark({ input, output, configPath })
```

| Name         | Type   | Description                                                                                                                                                     |
| ------------ | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `input`      | string | Directory where to read from                                                                                                                                    |
| `output`     | string | Directory where to save the generated HTML                                                                                                                      |
| `configPath` | string | (optional) Filepath of the stylemark YAML configuration file, defaults to `.stylemark.yml` within the input directory. See [Configuration](#configuration-file) |

Example:

```js
stylemark({
    input: "~/git/acme-source-code",
    output: "~/acme-style-guide",
    configPath: "~/acme-source-code/config/stylemark.yml",
})
```

### On the command-line

```sh
stylemark -i <input> -o <output> -c <configPath> -w [<delay>] -b [<port>]
```

| Name | Description                                                                                                                                                                                                        |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `-i` | Directory where to read from                                                                                                                                                                                       |
| `-o` | Directory where to save the generated HTML                                                                                                                                                                         |
| `-c` | (optional) Filepath of the stylemark YAML configuration file, defaults to `.stylemark.yml` within the input directory. See [Configuration](#configuration-file)                                                    |
| `-w` | (optional) Will watch for file changes in `<input>` and rebuild the style guide, waiting at least `<delay>` milliseconds between successive changes (defaults to `2000`)                                           |
| `-b` | (optional) Will open the style guide in your default browser at `http://localhost:<port>` and will automatically reload it when the style guide is updated. The port will be chosen automatically if not provided. |

**Example:** Build a style guide from `path/to/source/code` with a custom config file location, and save the generated HTML to `path/to/style/guide`

```sh
stylemark -i path/to/source/code -o path/to/style/guide -c ~/acme-source-code/config/stylemark.yml
```

**Example:** Build and open the style guide in a browser, and automatically rebuild and reload it when the source code is modified

```sh
stylemark -i path/to/source/code -o path/to/style/guide -w -b
```

### Configuration file

The Stylemark configuration file is a [YAML](https://en.wikipedia.org/wiki/YAML) file that contains settings to use when generating the HTML style guide.

**NOTE:** All paths are relative to root project directory of the configuration file (ie. the first ancestor directory that contains `package.json`).

```yaml
name: Name of the style guide

excludeDir: (optional) Regex pattern (in double quotes) or list of directories to exclude; .git and node_modules are always excluded
match: (optional) Regex pattern or list of files to process; by default, common source files are included

assets: (optional) List of relative file/directory paths to copy and mirror in the generated style guide

theme:
    logo: (optional) Filepath or URL of your logo
    css: (optional) List of any CSS files to include in the <head> of the generated styleguide; see Theming section
    js: (optional) List of any JS files to include in the <body> of the generated styleguide; see Theming section
    sidebar:
        background: (optional) Background of the sidebar; any valid CSS background property allowed, but hex colors must be quoted
        textColor: (optional) Text color of the sidebar; any valid CSS color property allowed, but hex colors must be quoted

examples:
    css: (optional) List of any CSS files to include in the <head> of each rendered example
    js: (optional) List of any JS files to include in the <head> of each rendered example
    doctypeTag: (optional) HTML doctype to use for each rendered example; defaults to "<!doctype html>"
    htmlTag: (optional) <html> tag to use for each rendered example; defaults to "<html>"
    bodyTag: (optional) <body> tag to use for each rendered example; defaults to "<body>"
    headHtml: (optional) HTML to insert before the closing </head> tag for each rendered example
    bodyHtml: (optional) HTML template of the example; the example's HTML content will be inserted in place of "{html}"

webpackAppPath: For Webpack apps (esp. React, Angular, etc.), this is the `output.library` value in your webpack config
emberAppName: For Ember apps, this is the name of the Ember app exported to the window object

order: (optional) See Ordering section
```

#### Ordering

The relative order of categories can be defined by prefixing a category name with `+`, `-`, or nothing:

-   Categories prefixed with `+` will be listed first
-   Categories prefixed with `-` will be listed last
-   Unprefixed categories will be listed in between

Omitted categories are ordered as if they were included but unprefixed.

Within each of the `+`-, `-`-, and un-prefixed groups, the specified order will be preserved. Example:

```
order:
- +Getting Started
- +Overview
- +Grid
- Topography
- -Extras
- -Other
```

#### Theming

The look and feel of the generated styleguide can be customized in the `theme` section of the config.

For example:

```
theme:
    css:
    - theme/theme.css

    js:
    - theme/theme.js

    sidebar:
        background: rgb(200, 0, 0)
        textColor: "#fff"
```

With that configuration, Stylemark will include `theme/theme.css` and `theme/theme.js` in the generated styleguide. Note that the `background` and `textColor` styles defined in the `sidebar` section will override any similar styles set in `theme/theme.css`.

Stylemark includes a number of CSS class hooks you can use to style specific elements. These CSS classes all start with `theme-` and include:

-   `theme-content`: The main scrollable page content
-   `theme-content-category`: Set of elements that make up a category
-   `theme-content-element`: An element, including its title and documentation
-   `theme-content-element-description`: An element's documentation, not including its title
-   `theme-content-element-title`: An element's title
-   `theme-content-element-source`: An element's source filepath container
-   `theme-content-element-source-label`: The text label of an element's source filepath
-   `theme-content-element-source-path`: The filepath string of an element's source filepath
-   `theme-mobile-nav`: The navigation view visible on smaller viewports
-   `theme-mobile-nav-select`: The `<select>` tag for the navigation dropdown visible on smaller viewports
-   `theme-page`: The entire page, including the content and sidebar
-   `theme-sidebar`: The sidebar
-   `theme-sidebar-categories`: The set of categories in the sidebar
-   `theme-sidebar-category`: A category in the sidebar, including its elements
-   `theme-sidebar-category-title`: A sidebar category's title
-   `theme-sidebar-element`: An element within a sidebar category
-   `theme-sidebar-footer`: Sidebar footer
-   `theme-sidebar-header`: Sidebar header
-   `theme-sidebar-header-logo`: Sidebar header logo
-   `theme-sidebar-header-title`: Sidebar header title that contains the styleguide name
-   `theme-sidebar-search`: Sidebar search module
-   `theme-sidebar-search-no-results`: Text that appears when no sidebar search results are found

**IMPORTANT:** Use only these `theme-` classes when customizing your styleguide. Relying on any other internal classes will result in your styles breaking when those internal classes change or are removed.

#### Example configuration

Here's a sample configuration with all options provided:

```yaml
name: Acme Design

excludeDir:
    - dist
    - docs

assets:
    - dist
    - fonts

theme:
    logo: assets/brand/logo.png

    css:
        - theme/theme.css

    js:
        - theme/theme.js

    sidebar:
        background: "#3b2a55"
        textColor: "#fff"

examples:
    css:
        - dist/css/app.min.css

    js:
        - https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js
        - dist/js/app.min.js

    doctypeTag: <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
    htmlTag: <html id="acme">
    bodyTag: <body class="acme-body">

    headHtml: |
        <meta name="google-site-verification" content="52cae…">
        <script>
            window.disableRouting = true;
        </script>

    bodyHtml: |
        <div style="padding: 20px">
            {html}
        </div>

order:
    - +Introduction
    - +Installation
    - -Credits
```
