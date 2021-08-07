# prismr: PrismJS for RMarkdown HTML

## Motivation

Sometime between the release of RStudio 1.3 (May 2020) which shipped with Pandoc
2.7.3, and the release of RStudio 1.4 (January 2021) which shipped with Pandoc
2.11, syntax highlighting went to :poop: (see example
[here](https://adamoshen.github.io/prismr/skylighting/)).

I suspect this was due to some changes in functionality in Skylighting, Pandoc's
syntax highlighter, where token definitions were changed but theme files remained
untouched. As such, many tokens were displayed with minimal highlighting as if
they were just plain text.

My previous efforts to circumvent this situation led me to create
[my own highlight theme](https://github.com/adamoshen/adam-highlight-theme)
through trial and error in order to:

- create a new colour theme
- obtain a similar amount of highlighting as in Pandoc <= 2.7.3

While this manually-edited highlight theme worked well for PDF output,
it did not work so well for HTML output. To use this custom highlight theme
with RMarkdown in RStudio, I had to "trick" RMarkdown/Pandoc into thinking that
I was using a custom template file, despite using one that was identical to
the default template. It was in this manner that RMarkdown was able to accept
the custom highlight theme and pass it to Pandoc. However, in doing so, MathJax
ended up getting disabled when trying to create self-contained HTML output.

After playing around a bit with blogdown, Hugo, and highlightJS on my personal
website, I was impressed with how beautiful things could be when Skylighting
wasn't allowed to touch my beautiful work. I was convinced that there must exist
a better way to assuage my highlighting needs.

I had initially come across [PrismJS](https://github.com/PrismJS/prism) in early
2021 as a result of my dissatisfaction with my first HTML document produced
in RStudio 1.4. However, their website looked sketchy AF, as if I had taken
a time machine back to the early 2000s, so I wasn't sure if it was legitimate.
Like, wtf is with the skull and crossbones at the bottom of their website???

I recently became brave enough to read their documentation and figure out how to
incorporate it into RMarkdown HTML while keeping all the existing features
and simplicity of the basic RMarkdown HTML output. With some modifications to
PrismJS' existing definitions for the R language, and some extra additions to
support highlighting of RMarkdown output (compare the
[original](https://github.com/adamoshen/prismr/blob/main/docs/default-theme/utils/prism.js)
with the one I've [modified](https://github.com/adamoshen/prismr/blob/main/utils/prismr.js)
and scroll to the very bottom), I can say without a doubt that PrismJS does a
very good job! In addition, PrismJS is easy to implement, lightweight, and it is
easy to create and customise themes.

## Minimum requirements

- rmarkdown (obviously)
- knitr (>= 1.16)

## Usage

### Recommended workflow

```shell
.
├── css
│   └── adam-one-dark.css
├── utils
│   ├── header.html
│   └── prismr.js
├── index.html
└── index.Rmd
```

### Minimal YAML header

```yaml
---
output:
  html_document:
    highlight: null
    css: css/adam-one-dark.css
    includes:
      in_header: utils/header.html
---
```

As this is based on `rmarkdown::html_document()`, it naturally supports all
the usual arguments (including MathJax!).

### Knitr setup chunk

The following code should be included in the setup code chunk at the beginning
of your `.Rmd` file:

```r
knitr::opts$chunk_set(class.source="language-r", class.output="language-r")
```

This enables highlighting in *both* source code chunks and output chunks. This
modifies the HTML of the source code chunks to have format:

```html
<pre class="r language-r"><code>
  ...
</code></pre>
```

and output code chunks to have format:

```html
<pre class="language-r"><code>
  ...
</code></pre>
```

Having `class="language-r"` tells PrismJS to highlight these chunks according to
the definitions supplied in `utils/prismr.js`. Hightlighting can be disabled on
a per-chunk basis by supplying the chunk options
`class.source="language-none"` or `class.output="language-none"`.

## Support for other languages

As PrismJS supports many languages, the `utils/prismr.js` file can be modified
by appending the contents of the auto-generated `.js` file from the
[PrismJS downloads page](https://prismjs.com/download.html#themes=prism)
(append only the language definitions found near the end of the file &mdash;
the rest of the file remains identical).

As Python is now supported by `rmarkdown` and `knitr` via the `reticulate`
package, highlighting of Python source chunks can be accomplished by setting the
chunk (or global) option, `class.source="language-python"`.

## Additional themes

Additional themes can be obtained from
[PrismJS/prism-themes](https://github.com/PrismJS/prism-themes). To ensure
that the theme will support the highlighting of RMarkdown output, you will need
to make sure the token definitions in the `.js` file correspond to the tokens
in the `.css` file. For example, in my `prismr.js` file, I have defined the
token `'output'`, which corresponds to `.token.output` in the `.css` file.

You can also build your own theme by modifying the included `.css` files.

## Session info

- RStudio 1.4.1106
    - rmarkdown 2.9
    - knitr 1.31
- Pandoc 2.14.1 (a separate installation)
    - Skylighting 0.11 (included in installation)
