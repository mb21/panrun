# Panrun

Minimal script that runs [pandoc](http://pandoc.org/) with the options it finds in the YAML metadata of the input markdown file. For example:

    panrun input.md

with the following `input.md`:

    ---
    title: my document
    output:
      html:
        standalone: true
        toc: true
        toc-depth: 2
      latex:
        toc: true
        toc_depth: 3
    ---

    # my content

Will execute `pandoc input.md --standalone --toc --toc-depth 2`.


## Usage

    panrun input-file [pandoc-options]

You can also supply more options, but only _after_ the input file. They will be forwarded to pandoc. Panrun also looks at the `-o`, `--output`, `-t` and `--to` options to determine the output format. For example:

    panrun input.md -t latex -o test.pdf

Panrun will only look at the YAML in the first input-file, but more are passed along to pandoc:

    panrun 01.md 02.md 03.md -o output.pdf

Thus `panrun *.md` will work, as long as the YAML is found in the alphabetically first file.

The input-file doesn't even have to be a markdown file. As long as it starts with a YAML block, it should work.


## Design

- Panrun should run with no dependencies except `ruby >= 2.3.3`, which is the builtin in macOS 10.13.
- Fortunately, Ruby comes with a YAML parser, which is the same Jekyll uses.
- Options unknown to your installed pandoc version are ignored.
- The idea is to be somewhat compatible with [rmarkdown's document format](https://bookdown.org/yihui/rmarkdown/output-formats.html). Therefore you can use, for example, either the `html` or `html_document` key (or even `pdf_document` or `slidy_presentation`), or either `toc-depth` or `toc_depth`, and the value of `pandoc_args` is also passed on. (However, as opposed to rmarkdown, panrun doesn't do anything more than passing on the options it finds.) Question: is this useful to anyone, or does this introduce more confusion, since a lot of rmarkdown-options will be silently ignored?
- If you're looking for more than a simple wrapper script, have a look at [panzer](https://github.com/msprev/panzer) or [pandocomatic](https://github.com/htdebeer/pandocomatic).
- Work in progress, look at the source!
- Possible TODOs:
  - [ ] Look for non-format specific options directly in the `output` mapping.
  - [ ] Change usage to `panrun [options] input.md [pandoc-options]`. Options could be `-m theme.yaml`, which would also look for files in `~/.panrun`?
  - [ ] What about options that can be used multiple times, like `-V`? Check whether value is array...


## Installation

[Download panrun](https://raw.githubusercontent.com/mb21/panrun/master/panrun) and place it somewhere on your `PATH`. Make sure the file has no extension and make it executable. On macOS/Linux:

    chmod +x ./panrun
