# Table of Contents

* [markdown-include](#markdown-include)
  * [Compile your markdown files](#compile-your-markdown-files)
  * [Make a table of contents](#make-a-table-of-contents)
* [How To Install](#how-to-install)
* [How To Use From The Command Line](#how-to-use-from-the-command-line)
  * [markdown.json](#markdownjson)
* [How To Use As A Module](#how-to-use-as-a-module)
  * [API](#api)
* [How It Works](#how-it-works)


# markdown-include

markdown-include is built using Node.js and allows you to include markdown files into other markdown files using a C style include syntax.

## Compile your markdown files

markdown-include's main feature is that it allows you to include allows you to include markdown files into other markdown files, like so:

```
#include "markdown-file.md"
#include "another-markdown-file.md"
```

Assuming that `markdown.file.md` contents are:

```
Something in markdown file!
```

And assuming that `another-markdown-file.md` contents are: 

```
Something in another markdown file!
``` 

It would compile to:

```
Something in markdown file!
Something in another markdown file!
```

## Make a table of contents

Aside from compiling your markdown files, markdown-include can also build your table of contents.  This works by evaluating the heading tags inside of your files.  Since markdown works on using `#` for headings, this makes it easy to assemble table of contents from them.  The more `#` you have in front of your headings (up to 6) will decide how the table of contents is built.  Use one `#` and it's a top level navigation item... Use two `#` and it would be underneath the previous navigation item and so on.

For each heading that you would like to be included in a table of contents just add ` !heading` to the end of it.


# How To Install

markdown-include is available on npm for easy installation:

```
npm install markdown-include
```

Use the `-g` flag if you wish to install markdown-include globally on your system.  Use the `--save` flag to save in your `package.json` file in your local project.


# How To Use From The Command Line

markdown-include is very easy to use whether on the command line or in your own node project.  Each can help you compile your markdown files as you see fit.  markdown-include does require that you define a `markdown.json` file with your options for compile.  See below for all of the options available to you.

Run from the command line to compile your documents like so:

```
node_modules/bin/cli.js path/to/markdown.json
```


## markdown.json

`markdown.json` can be populated with the following options:

| Option                    | Type    | Description                                                                |
|:-------------------------:|:-------:|:--------------------------------------------------------------------------:|
| `build`                   | String  | File path of where everything should be compiled, like `README.md`.        |
| `files`                   | Array   | Array of files to to compile.                                              |
| `tableOfContents`         | Object  | Object to hold options for table of contents generation.                   |
| `tableOfContents.heading` | String  | Heading for table of contents (use markdown syntax if desired).            |
| `tableOfContents.lead`    | String  | What navigation items in table of contents lead with.  If value is `number` will add numbers before each item and subitem.  If not, will add asterisks.  Refer to markdown syntax to understand the difference. |
# How To Use As A Module

Just require in your node project:

```
var markdownInclude = require('markdown-include');
```

From there, you can use markdown-include's API to fit your needs.


## API

When using as a module, markdown-include offers an API for you to work with markdown files as detailed below:

### `buildLinkString(str)`

```javascript
var markdownInclude = require('markdown-include');
markdownInclude.buildLinkString("My Link String"); // my-link-string
```

| Parameter(s)    | Type     | Returns  | Description                                                            |
|:---------------:|:--------:|:---------|:----------------------------------------------------------------------:|
| `string`        | `String` | `String` | File path of where everything should be compiled, like `README.md`.    |

---

# How It Works

markdown-include works by recursively going through files based on the tags that are found.  For instance, consider the following in a `_README.md` file:

```
#include "first-file.md"
```

Let's also consider that `first-file.md` contains the following:

```
#include "third-file.md"
```

Let's also consider that `markdown.json` contains the following:

```json
{
	"build" : "README.md",
	"files" : ["_README.md"]
}
```

markdown-include will first read the contents of `_README.md` and look for include tags.  It will find `#include "first-file.md"` first.  From there it will parse the tag, open `first-fild.md` and find include tags in that file.  This process continues until no more include tags are found.  

At that point it will start over in the original file and parse other include tags if they exist.  Along the way, markdown-include will parse each file and keep a record of the contents.  Once the process is finished, a file will be written in `README.md` with all of the compiled content.

As you can see, you only need to reference one file which would be `_README.md`.  We didn't need to add `first-file.md` or `third-file.md`... markdown-include does that compiling for us by making an internal chain.

**NOTE**:  You must provide markdown-include with the entire file path you're trying to find in your working directory.  For example, if `first-file.md` and `third-file.md` were in the `docs` directory together and `first-file.md` was trying to include `third-file.md` you would need to do the following in `first-file.md`:

```
#include "docs/third-file.md"
```

This is because markdown-include doesn't make any assumptions about where your files are.  Use the correct paths or you could run into errors!