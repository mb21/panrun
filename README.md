# Panrun

Minimal script that runs [pandoc](http://pandoc.org/) with the options it finds in the YAML metadata of the input markdown file. For example:

    panrun input.md

with the following `input.md`:

    ---
    title: my document
    output:
      html_document:
        standalone: true
        toc: true
        toc-depth: 2
      pdf_document:
        toc: true
        toc_depth: 3
    ---

    # my content

Will execute `pandoc input.md --standalone --toc --toc-depth 2`.

You can also supply more options, but only _after_ the input file. They will be forwarded to pandoc. Panrun also looks at the `-o`, `--output`, `-t` and `--to` options to determine the output format. For example:

    panrun input.md -o test.pdf


## Design

- Panrun should run with no dependencies except `ruby >= 2.3.3`, which is the builtin in macOS 10.13.
- Fortunately, Ruby comes with a YAML parser, which is the same Jekyll uses.
- The idea is to be somewhat compatible with [rmarkdown's document format](https://rmarkdown.rstudio.com/html_document_format). Thus you can use e.g. either `toc-depth` or `toc_depth` and the value of `pandoc_args` is also passed on. However, as opposed to rmarkdown, panrun doesn't do anything more than passing on the options it finds.
- Options unknown to your installed pandoc version are ignored.
- If you're looking for more than a simple wrapper script, have a look at [panzer](https://github.com/msprev/panzer) or [pandocomatic](https://github.com/htdebeer/pandocomatic).
- Work in progress, look at the source!
- TODO: set some variable, e.g. from the [pdf_document format](https://rmarkdown.rstudio.com/pdf_document_format#latex_options)?


## Installation

[Download panrun](https://raw.githubusercontent.com/mb21/panrun/master/panrun) and place it somewhere on your `PATH`. Make sure the file has no extension and make it executable. On macOS/Linux:

    chmod +x ./panrun
