# Panrun

Minimal script that runs [pandoc](http://pandoc.org/) with the options it finds in the YAML metadata of the input markdown file. For example:

    panrun input.md

with the following `input.md`:

    ---
    title: my document
    output:
      html:
        standalone: true
        output: test.html
        include-in-header:
          - foo.css
          - bar.js
      latex:
        toc: true
        toc-depth: 3
        output: test.pdf
        template: letter.tex
        metadata:
          fontsize: 12pt
    ---

    # my content

Will execute:

    pandoc test.md --standalone --output test.html --include-in-header foo.css --include-in-header bar.js

Note how panrun defaults to using the first key in the YAML, in this case `html`.


## Usage

    panrun input-file [pandoc-options]

You can also supply more options, but only _after_ the input file. They will be forwarded to pandoc. Panrun also looks at the `-o` (`--output`) and `-t` (`--to`) options to determine the output format. For example:

    panrun input.md -t latex -o test.pdf

Panrun will only look at the YAML in the first input-file, but more are passed along to pandoc:

    panrun 01.md 02.md 03.md -o output.pdf

Thus `panrun *.md` will work, as long as the YAML is found in the alphabetically first file.

The input-file doesn't even have to be a markdown file. As long as it starts with a YAML block, it should work.


### Defaults and document types

If you put some YAML in `~/.panrun/default.yaml` (see `panrun -h` for the Windows location), panrun will merge this with the YAML in your input file and add the `--metadata-file` option when calling pandoc. The YAML should be in the same format as always, for example:

    ---
    author: Always Me
    output:
      html:
        standalone: true
    ---

Finally, you can e.g. put `type: letter` in the YAML of your input document. In that case, panrun will look for `~/.panrun/letter.yaml` instead of `default.yaml`.


## Design

- Panrun should run with no dependencies except pandoc and `ruby >= 2.3.3`, which is the builtin in macOS 10.13.
- Fortunately, Ruby comes with a YAML parser, which is the same one Jekyll uses.
- Panrun doesn't hardcode or assume anything about the options. It simply asks your installed pandoc which options it supports (through `pandoc --bash-completion`) and ignores the unknown options in your YAML.
- The idea is to be somewhat compatible with [rmarkdown's document format](https://bookdown.org/yihui/rmarkdown/output-formats.html). Therefore you can use, for example, either the `html` or `html_document` key (or even `pdf_document` or `slidy_presentation`), or either `toc-depth` or `toc_depth`, and the value of `pandoc_args` is also passed on. (However, as opposed to rmarkdown, panrun doesn't do anything more than passing on the options it finds.) Question: is this useful to anyone, or does this introduce more confusion, since a lot of rmarkdown-options will be silently ignored?
- If you're looking for more than a simple wrapper script, have a look at [panzer](https://github.com/msprev/panzer) or [pandocomatic](https://github.com/htdebeer/pandocomatic).
- If you're wondering whether this functionality will soon be part of pandoc itself, the answer is [probably not](https://github.com/jgm/pandoc/issues/4627#issuecomment-422108494).
- Look at the source, it's really quite minimal! (In the end, I couldn't resist adding another ~40 lines of code for the defaults functionality...)
- Possible TODOs:
  - [ ] Expand usage to `panrun [options] input.md [pandoc-options]`, so we could pass the target format to panrun without worrying about it having the same name as a pandoc format. For example, `panrun -t html_pdf input.md` could look for the `html_pdf` key in the `output` field in the YAML.
  - [ ] Tests
  - [ ] Look for non-format specific options directly in the `output` mapping?


## Installation

1. [Download panrun](https://raw.githubusercontent.com/mb21/panrun/master/panrun)
2. Place the file somewhere on your `PATH` (e.g. in `/usr/local/bin/`)
3. Make sure the file has no extension and make it executable. On macOS/Linux (for Windows [read this](https://stackoverflow.com/questions/1422380/)):

       chmod +x ./panrun
