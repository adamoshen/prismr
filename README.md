# prismr: PrismJS for RMarkdown HTML (and `xaringan`)

## Minimum requirements

- rmarkdown (obviously)
- knitr (>= 1.16)

## Usage - HTML documents

### Recommended workflow

```shell
.
├── css
│   └── adam-one-dark.css
├── utils
│   ├── header.html
│   └── prismr.js
├── index.html
└── index.Rmd
```

### Minimal YAML header

```yaml
output:
  html_document:
    highlight: null
    css: css/adam-one-dark.css
    includes:
      in_header: utils/header.html
```

This configuration is compatible with:

- `rmarkdown::html_document`
- `bookdown::html_document2`
- `bookdown::gitbook`

This configuration is *not* compatible with:

- `bookdown::bs4_book`

### Knitr setup chunk

The following code should be included in the setup code chunk at the beginning of your `.Rmd` file:

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

## Usage - `xaringan` slides

The `prismr.js` and corresponding css files can be included as above, or via
`htmltools::tagList` and `htmltools::htmlDependency` as shown in the example
[here](https://github.com/adamoshen/prismr/blob/main/docs/xaringan/index.Rmd).
The `highlightStyle` should also be set to `null` to disable highlighting via
`highlight.js`.

Highlighting is enabled on a per-slide basis by adding `language-*` to the slide
class, e.g. `class: center, inverse, language-r`.

It should be noted that the monospace font is defined once in the corresponding
Prism css file, and once again in the presentation css file. In the included example,
the font definition in the Prism css file takes precendence, so this should either
be removed or modified to match the monospace font defined for the presentation.


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

- RStudio 2022.02.3+492 (Prairie Trillium)
    - rmarkdown 2.14
    - knitr 1.39
    - xaringan 0.24
- Pandoc 2.14.1 (a separate installation)
    - Skylighting 0.11 (included in installation)
