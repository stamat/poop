# 💩 Poops
> A super simple bundler for simple web projects.

----

Intuitive with a minimal learning curve and minimal docs, utilising the most efficient transpilers and compilers available (like [dart-sass](https://sass-lang.com/dart-sass) and [esbuild](https://esbuild.github.io/)) Poops aims to be the simplest bundler option there is. If it's not, please do contribute so we can make it so! 🙏 All ideas and contributions are welcome.

It uses a simple config file where you define your input and output paths and it poops out your bundled files. Simple as that.

## Features

* Bundles SCSS/SASS to CSS
* Uses [dart-sass](https://sass-lang.com/dart-sass) for SCSS/SASS bundling
* CSS minification with Nanocss and Autoprefixer via [postcss](https://postcss.org/)
* Bundles JS/TS to IIFE/ESM/CJS
* Uses [esbuild](https://esbuild.github.io/) for ES bundling
* Uses [terser](https://terser.org/) for JS minification and mangling
* Can produce minified code simultaneously with non-minified code!
* Supports multiple input and output paths
* Resolves node modules
* Can add a templatable banner to output files (optional)
* Supports source maps (optional)
* Supports minification (optional)
* Static site generation with [nunjucks](https://mozilla.github.io/nunjucks/) templating (optional)
* Has a configurable local server (optional)
* Rebuilds on file changes (optional)
* Live reloads on file changes (optional)

## Quick Start
You can install Poops globally:

```bash
npm i -g poops
```

or locally:

```bash
npm i -D poops
```

If you have installed Poops globally, create a `poops.json` configuration file in the project root (see [Configuration](#configuration) on how to configure) and run:

```bash
poops
```

or pass a custom config. This is usefull when you have multiple environments:

```bash
poops yourAwesomeConfig.json
```

If you have installed Poops locally you can run it with `npx poops` or add a script to your `package.json`:

```json
{
  "scripts": {
    "build": "npx poops"
  }
}
```

## Configuration

Configuring Poops is simple 😌. Let's presume that we have a `example/src/scss` and `example/src/js` directories and we want to bundle the files into `example/dist/css` and `example/dist/js`. If you also have markup files, you can use [nunjucks](https://mozilla.github.io/nunjucks/) templating engine to generate HTML files from your templates. Let's presume that we have a `example/src/markup` directory and we want to generate HTML files in the root of the your directory.

Just create a `poops.json` file in the root of your project and add the following (you can see this sample config in this repo's root):

```json
{
  "scripts": [{
    "in": "example/src/js/main.ts",
    "out": "example/dist/js/scripts.js",
    "options": {
      "sourcemap": true,
      "minify": true,
      "justMinified": false,
      "format": "iife",
      "target": "es2019",
      "mangle": true
    }
  }],
  "styles": [{
    "in": "example/src/scss/index.scss",
    "out": "example/dist/css/styles.css",
    "options": {
      "sourcemap": true,
      "minify": true,
      "justMinified": false
    }
  }],
  "markup": {
    "in": "example/src/markup",
    "out": "/",
    "site": {
      "title": "Poops",
      "description": "A super simple bundler for simple web projects."
    }
  },
  "banner": "/* {{ name }} v{{ version }} | {{ homepage }} | {{ license }} License */",
  "serve" : {
    "port": 4040,
    "base": "/"
  },
  "livereload": true,
  "watch": [
    "src"
  ],
  "includePaths": [
    "node_modules"
  ]
}
```

All config properties are optional except `scripts`, `styles` or `markups`. You have to specify at least one of them. If you don't have anything to consume, you won't poop. 💩

You can freely remove the properies that you don't need. For example, if you don't want to run a local server, just remove the `serve` property from the config.

### Scripts

Scripts are bundled with [esbuild](https://esbuild.github.io/). You can specify multiple scripts to bundle. Each script has the following properties:

* `in` - the input path, can be an array of file paths, but please just use one file path per script
* `out` - the output path, can be a directory or a file path, but please just use it as a filename
* `options` - the options for the bundler. You can apply most of the esbuild options that are not in conflict with Poops. See [esbuild's options](https://esbuild.github.io/api/#build-api) for more info.

Options:
* `sourcemap` - whether to generate sourcemaps or not, sourcemaps are generated only for non-minified files since they are useful for debugging. Default is `false`
* `minify` - whether to minify the output or not, minification is performed by Terser and is only applied to non-minified files. Default is `false`
* `mangle` - whether to mangle the output or not, mangling is performed by Terser and this is the only Terser oprion. Default is `false`
* `justMinified` - whether you want to have a minified file as output only. Removes the non-minified file from the output. Useful for production builds. Default is `false`
* `format` - the output format, can be `iife` or `esm` or `cjs` - this is a direct esbuild option
* `target` - the target for the output, can be `es2018` or `es2019` or `es2020` or `esnext` for instance - this is a direct esbuild option


`scripts` property can accept an array of script configurations or just a single script configuration. If you want to bundle multiple scripts, just add them to the `scripts` array:

```json
{
  "scripts": [
    {
      "in": "src/js/main.ts",
      "out": "dist/js/scripts.js",
      "options": {
        "sourcemap": true,
        "minify": true,
        "justMinified": false,
        "format": "iife",
        "target": "es2019",
        "mangle": true
      }
    },
    {
      "in": "src/js/other.ts",
      "out": "dist/js/other.js",
      "options": {
        "sourcemap": true,
        "minify": true,
        "justMinified": false,
        "format": "iife",
        "target": "es2019",
        "mangle": true
      }
    }
  ]
}
```

As noted earlier, if you don't want to bundle scripts, just remove the `scripts` property from the config.

### Styles

Styles are bundled with [Dart Sass](https://sass-lang.com/dart-sass). You can specify multiple styles to bundle. Each style has the following properties:

* `in` - the input path, accepts only a path to a file
* `out` - the output path, can be a directory or a file path, but please just use it as a filename
* `options` - the options for the bundler.

Options:
* `sourcemap` - whether to generate sourcemaps or not, sourcemaps are generated only for non-minified files since they are useful for debugging. Default is `false`
* `minify` - whether to minify the output or not, minification is performed by `cssnano` and is only applied to non-minified files. At the minification step, `autoprefixer` is also applied. Default is `false`
* `justMinified` - whether you want to have a minified file as output only. Removes the non-minified file from the output. Useful for production builds. Defaults to `false`.

`styles` property can accept an array of style configurations or just a single style configuration. If you want to bundle multiple styles, just add them to the `styles` array:

```json
{
  "styles": [
    {
      "in": "src/scss/main.scss",
      "out": "dist/css/styles.css",
      "options": {
        "sourcemap": true,
        "minify": true,
        "justMinified": false
      }
    },
    {
      "in": "src/scss/other.scss",
      "out": "dist/css/other.css",
      "options": {
        "sourcemap": true,
        "minify": true,
        "justMinified": false
      }
    }
  ]
}
```

As noted earlier, if you don't want to bundle styles, just remove the `styles` property from the config.

### Markups 🚧

Poops can generate static pages for you. This feature is still under development, but available for testing from the  v1.0.2. Your markup is templated with [nunjucks](https://mozilla.github.io/nunjucks/). You can specify multiple markup directories to template. **It's currently recommended to specify only one markup directory since this feature is still WIP 🚧.** Each markup directory has the following properties:

* `in` - the input path, can be a directory or a file path, but please just use it as a directory path
* `out` - the output path, can be only a directory path (for now)
* `site` (otpional) - global data that will be available to all templates in the markup directory. Like site title, description, social media links, etc. You can then use this data in your templates `{{ site.title }}` for instance.
* `data` (optional) - is an array of JSON data files, that will be transformed into JS objects and will be available to all templates in the markup directory. If you provide a path to a file for instance `links.json` with a `facebook` property, you can then use this data in your templates `{{ links.facebook }}`. The base name of the file will be used as the variable name, with spaces, dashes and dots replaced with underscores. So `the awesome-links.json` will be available as `{{ the_awesome_links.facebook }}` in your templates.
* `includePaths` (optional and WIP 🚧) - an array of paths to directories that will be added to the nunjucks include paths. Useful if you want to include templates from other directories. For instance, if you have a `includes` directory with some templates that you want to include in your markup, you can add it to the include paths and then include the templates like this `{% include "header.njk" %}`, without specifying the full path to the template. All paths that begin with an underscore `_` are ignored by default are currently all interpreted as `includePaths` automatically. This will change in the future, to procide ignore patterns for the markup directories.

**💡 NOTE:** In order to pass the current version to your markup files, Poops reads your `package.json` if it exists in your working directory and sets the golobal variable `package` to the parsed JSON. So you can use it in your markup files, for example like this: `{{ package.version }}`.

### Banner (optional)

Here you can specify a banner that will be added to the top of the output files. It is templatable via mustache. The following variables are available from your project's `package.json`:

* `name`
* `version`
* `homepage`
* `license`
* `author`
* `description`


Here is a samle banner template.
```
/* {{ name }} v{{ version }} | {{ homepage }} | {{ license }} License */
```

You can always pass just a string, you don't have to template it.

If you don't want to add a banner, just remove the `banner` property from the config.

### Local Server (optional)
Sets up a local server for your project.

Server options:
* `port` - the port on which the server will run
* `base` - the base path of the server, where your HTML files are located

If you don't want to run a local server, just remove the `serve` property from the config.

### Live Reload (optional)
Sets up a livereload server for your project.

Live reload options:
* `port` - the port on which the livereload server will run
* `exclude` - an array of files and directories to exclude from livereload

`livereload` can only be `true`, which means that it will run on the default port (`35729`) or you can specify a port:

```json
{
  "livereload": {
    "port": whateverPortYouWant
  }
}
```

You can also exclude files and directories from livereload:

```json
{
  "livereload": {
    "exclude": [
      "some_directory/**/*",
      "some_other_directory/**/*"
    ]
  }
}
```

If you don't want to run livereload, just remove the `livereload` property from the config, or set it to false.

### Watch (optional)
Sets up a watcher for your project which will rebuild your files on change.

`watch` propery accepts an array of paths to watch for changes. If you want to watch for changes in the `src` directory, just add it to the `watch` array:

```json
{
  "watch": [
    "src"
  ]
}
```

If you don't want to watch for file changes, just remove the `watch` property from the config.

### Include Paths (optional)
This property is used to specify paths that you want to resolve your imports from. Like `node_modules`. You don't need to specify the `includePaths`, `node_modules` are included by default. But if you do specify `includePaths`, you need to include `node_modules` as well, since this change will override the default behaviour.

Same as `watch` property, `includePaths` accepts an array of paths to include. If you want to include `lib` directory for instance, just add it to the `includePaths` array:

```json
{
  "includePaths": [
    "node_modules", "lib"
  ]
}
```

## Todo

* [ ] Run esbuild for each input path individually if there are multiple input paths
* [ ] Styles `in` should be able to support array of inputs like we have it on scripts
* [x] Add nunjucs static templating
  * [ ] Refactor nunjucks implementation
  * [ ] Complete documentation for nunjucks
  * [ ] Add markdown support
  * [ ] Front Matter support
  * [ ] Future implementation: alternative templating engine liquidjs
  * [ ] Future implementation: posts and custom collections, so we can have a real static site generator
* [ ] Refactor!!!!
* [ ] Add more argv options like config creation, etc.

## Why?

Why doesn't anyone maintain GULP anymore? Why does Parcel hate config files? Why are Rollup and Webpack so complex to setup for simple tasks? Vite???? What's going on?

I'm tired... Tired of bullshit... I just want to bundle my scss/sass and/or my js/ts to css and iife/esm js, by providing input and output paths for both/one. And to be able to have minimal easily maintainable dependancies. I don't need plugins, I'll add the features manually for the practice I use. That's it. The f**king end.

To better illustrate it, here is a sample diff of Poops replacing Rollup:

![Screenshot 2023-07-03 at 16 34 32](https://github.com/stamat/poops/assets/1429864/6a8598e7-d188-4d9f-ae3c-5bfa3bbf78e9)

This is a bundler written by me for myself and those like me. Hopefully it's helpful to you too.

Love :heart: and peace :v:.
