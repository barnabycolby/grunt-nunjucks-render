grunt-nunjucks-render-alt
=========================

[![Build Status](https://travis-ci.org/barnabycolby/grunt-nunjucks-render-alt.svg?branch=master)](https://travis-ci.org/barnabycolby/grunt-nunjucks-render-alt)

This is a [grunt](http://gruntjs.com/) plugin to render [nunjucks](http://mozilla.github.io/nunjucks/) 
templates and strings. It takes data in `JSON` or `YAML` format and allows to configure *nunjucks* in
*Grunt* tasks.


Getting Started
---------------

This plugin requires Grunt `~0.4.1` and Node `~0.10.0`. Please first have a look at the 
[Getting Started](http://gruntjs.com/getting-started) guide of Grunt. 

You may install this plugin with this command:

```shell
npm install grunt-nunjucks-render-alt --save-dev
```

Once the plugin is installed, it may be enabled inside your `Gruntfile.js` with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-nunjucks-render-alt');
```


The "nunjucks_render" task
--------------------------

### Overview

In your project's Gruntfile, add a section named `nunjucks_render` to the data object 
passed into `grunt.initConfig()`:

```js
grunt.initConfig({
  nunjucks_render: {
    options: {
                        // task global options go here
    },
    your_target: {
      options: {
                        // target specific options go here
      },
      files: [
        {
          data:         // path to JSON or YAML file(s)
          src:          // path to template file(s)
          dest:         // path to output destination file(s)
          str:          // direct string(s) to parse (before templates by default)
          str_prepend:  // direct string(s) to parse and concat before templates
          str_append:   // direct string(s) to parse and concat after templates
        }
      ]
    }
  }
});
```

See the [dedicated page](http://gruntjs.com/configuring-tasks#files-array-format) to learn
how to build your `files` objects. Note that some properties are added here:

-   `data` to define a list of files to get the parsing data,
-   `str`, `str_prepend` and `str_append` to define some raw strings to parse and concatenate
    to the final rendering.

### Environment

A special [*nunjucks* environment loader](http://mozilla.github.io/nunjucks/api.html#loader) 
is designed by the plugin to use a full set of options when searching templates (original 
and included ones). The loader is defined in `lib/loader.js`.

The `template_path` environment variable will always contain the path of the current parsed
template (the current file), even for inclusions. The `template_date` environment variable 
will always contain the date of the current parsing, even for inclusions.

The plugin embeds the [date-filter](https://www.npmjs.com/package/nunjucks-date-filter) natively
to let you write formated dates:

    {{ my_date | date() }}          // with default format
    {{ my_date | date('YYY') }}     // with custom format

### Options

Options can be defined globally for the `nunjucks_render` task or for each target.
The target options will always overload global settings.

A "*template*" here is a raw template, defined as the `src` item of a target files, or a
*nunjucks* included template.

-   **searchPaths**
    -   Type: `String`, `Array`  
    -   Default value: `"."` (i.e. relative to your `Gruntfile.js`)
    -   One or more paths to be scanned by the template loader while searching *nunjucks* templates.
        By default, the loader will search in **all** directories of the root directory. If `baseDir`
        is defined and the template is found in it, this file will be used.

-   **baseDir**
    -   Type: `String`  
    -   Default value: `"."` (i.e. relative to your `Gruntfile.js`)
    -   Path to the directory in which *nunjucks* will first search templates. This directory
        name is striped from templates files `name` entry.

-   **extensions**
    -   Type: `String`, `Array`  
    -   Default value: `".j2"` (for *jinja2*)
    -   One or more file extensions to use by the template loader while searching *nunjucks* templates.
        This allows you to write `{% include my-partial %}` instead of `{% include my-partial.j2 %}`.

-   **autoescape**
    -   Type: `Boolean`  
    -   Default value: `false`
    -   Force data escaping by *nunjucks* (see <http://mozilla.github.io/nunjucks/api.html#configure>).

-   **watch**
    -   Type: `Boolean`  
    -   Default value: `true`
    -   Force *nunjucks* to watch template files updates (see <http://mozilla.github.io/nunjucks/api.html#configure>).

-   **data**
    -   Type: `Object`, `String` (filename), `Array` (array of filenames)
    -   Default value: `null`
    -   Can be used to fill in a default `data` value for a whole task or a target. This will
        be merged with "per-file" data (which have precedence).

-   **processData**
    -   Type: `Function`
    -   Default: `null`
    -   Define a function to transform data as a pre-compilation process (before to send them
        to *nunjucks*).

-   **name**
    -   Type: `RegExp`, `Function`
    -   Default value: `/.*/`
    -   A regular expression or function to build final template name. Default is the filename.

-   **asFunction**
    -   Type: `Boolean`
    -   Default value: `false`
    -   Use this to return the raw *precompiled* version of the *nunjucks* content instead of its
        final rendering.

-   **env**
    -   Type: `nunjucks.Environment`
    -   Default value: `null`
    -   A custom *nunjucks* environment to use for compilation (see <http://mozilla.github.io/nunjucks/api.html#environment>).

-   **strAdd**
    -   Type: `String` in "*prepend*" or "*append*"
    -   Default value: `"prepend"`
    -   The default process to execute on the `str` files entry. By default, strings will be
        parsed and *prepended* to final rendering (i.e. displayed before templates contents).
        Note that when this argument is `prepend`, the `str` items will be added AFTER any
        `str_prepend` other items while if it is `append`, the `str` items will be added
        BEFORE any other `str_append` items.

-   **strSeparator**
    -   Type: `String`
    -   Default value: `"\n"`
    -   A string used to separate parsed strings between them and from templates contents.

#### Examples

You can have a look at the `Gruntfile.js` of the plugin for various usage examples.

Define a base path for all parsed files:

```js
options: {
    baseDir: 'templates/to/read/'
},
files: {
    'file/to/output-1.html': 'file-1.j2',
    'file/to/output-2.html': 'file-2.j2',
    'file/to/output-3.html': 'file-3.j2'
}
```

Define a global `data` table for all parsed files:

```js
options: {
    data: {
        name: "my name",
        desc: "my description"
    }
},
files: {
    'file/to/output.html': 'template/to/read.j2'
}
```

Define a global JSON data file for all parsed files:

```js
options: {
    data: 'commons.json'
},
files: {
    'file/to/output-1.html': 'template/to/read-1.j2',
    'file/to/output-2.html': 'template/to/read-2.j2',
    'file/to/output-3.html': 'template/to/read-3.j2'
}
```

Define a global `data` table for all targets, over-written by a "per-target" data:

```js
options: {
    data: {
        name: "my name",
        desc: "my description"
    }
},
my_target: {
    files: {
        data:   { desc: "my desc which will over-write global one" },
        src:    'template/to/read.j2',
        dest:   'file/to/output.html'
    }
}
```

Define a full set of [grunt files](http://gruntjs.com/configuring-tasks#files-array-format):

```js
files: [
  {
    expand: true,
    src: 'template/to/read-*.j2',
    data: 'common-data.json',
    dest: 'dest/directory/'
  }
]
```

Usage of the `str` argument:

```js
files: {
    data:   { desc: "my desc which will over-write global one" },
    src:    'template/to/read.j2',
    dest:   'file/to/output.html',
    str:    "My nunjucks string with var: {{ username }}"
}
```

```js
files: {
    data:   { desc: "my desc which will over-write global one" },
    src:    'template/to/read.j2',
    dest:   'file/to/output.html',
    str:    [ "My first nunjucks string with var: {{ username }}", "My second nunjucks string with var: {{ username }}" ]
}
```


Use the module in your tasks
----------------------------

As this module defines "non-tasked" functions, you can use the followings in your own tasks
or modules.

### `NunjucksRenderFile`

    var NunjucksRenderFile = require('grunt-nunjucks-render-alt/lib/render-file');
    var content = NunjucksRenderFile( filepath , data[ , options ][ , nunjucks.Environment ]);

### `NunjucksRenderString`

    var NunjucksRenderString = require('grunt-nunjucks-render-alt/lib/render-string');
    var content = NunjucksRenderString( str , data[ , options ][ , nunjucks.Environment ]);


Related third-parties links
---------------------------

-   [Grunt](http://gruntjs.com/), a task runner
-   [Nunjucks](http://mozilla.github.io/nunjucks/), a templating engine for JavaScript
-   [Jinja2](http://jinja.pocoo.org/), the first inspiration of *Nunjucks*
-   [JSON](http://json.org/), the *JavaScript Object Notation*
-   [YAML](http://yaml.org/), a human friendly data serialization
-   [moment.js](http://momentjs.com/), a date/time manager
-   [nunjucks-date-filter](https://www.npmjs.com/package/nunjucks-date-filter), an implementation
    of *momentjs* for *nunjucks*


Contributing
------------

In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit 
tests for any new or changed functionality. Lint and test your code using [Grunt](http://gruntjs.com/).
