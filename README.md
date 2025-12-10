![Version](https://img.shields.io/endpoint?url=https://shield.abappm.com/github/abapPM/ABAP-HTML-Diff/src/zcl_htmldiff.clas.abap/c_version&label=Version&color=blue)

[![License](https://img.shields.io/github/license/abapPM/ABAP-HTML-Diff?label=License&color=success)](LICENSE)
[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-2.1-4baaaa.svg?color=success)](https://github.com/abapPM/.github/blob/main/CODE_OF_CONDUCT.md)
[![REUSE Status](https://api.reuse.software/badge/github.com/abapPM/ABAP-HTML-Diff)](https://api.reuse.software/info/github.com/abapPM/ABAP-HTML-Diff)

# ABAP HTML Diff

Library to highlight the content difference between two HTML strings (htmldiff).

This is a diffing library that understands HTML. Best suited for cases when you want to show a diff of user-generated HTML.

An enhancement was made so the code can also produce the diff of two texts (tags are treated like text).

NO WARRANTIES, [MIT License](LICENSE)

## Prerequisites

SAP Basis 7.4 or higher

## Usage

### Diffing HTML

The following produces the diff of two example HTML snippets:

```abap
DATA:
  lv_original TYPE string,
  lv_modified TYPE string,
  lv_diff     TYPE string,
  li_htmldiff TYPE REF TO zif_htmldiff.

lv_original = '\n'
  && '    <p>First paragraph.</p>\n'
  && '    <ul>\n'
  && '        <li>Item A</li>\n'
  && '        <li>Item B</li>\n'
  && '        <li>Item C</li>\n'
  && '    </ul>\n'
  && '    <img src="previous.jpg">\n'
  && '    <span>This is some interesting text.</span>\n'.
```
```abap
lv_modified = '\n'
  && '    <p>First paragraph.</p>\n'
  && '    <ul>\n'
  && '        <li>Item A</li>\n'
  && '        <li>Item B</li>\n'
  && '        <li>Item D</li>\n'
  && '    </ul>\n'
  && '    <img src="next.jpg">\n'
  && '    <span>This is some new text.</span>\n'.

REPLACE ALL OCCURRENCES OF '\n' IN lv_original WITH cl_abap_char_utilities=>newline.
REPLACE ALL OCCURRENCES OF '\n' IN lv_modified WITH cl_abap_char_utilities=>newline.

li_htmldiff = zcl_htmldiff=>create( ).

lv_diff = li_htmldiff->htmldiff(
  iv_before   = lv_original
  iv_after    = lv_modified
  iv_with_img = abap_false ).
```

Result:

```html
    <p>First paragraph.</p>
    <ul>
        <li>Item A</li>
        <li>Item B</li>
        <li>Item <del>C</del><ins>D</ins></li>
    </ul>
    <img src='previous.jpg'><img src='next.jpg'>
    <span>This is some <del>interesting</del><ins>new</ins> text.</span>
```

By setting the image parameter to true, you can also mark changed images as deletions or insertions:

```abap
lv_diff = li_htmldiff->htmldiff(
  iv_before   = lv_original
  iv_after    = lv_modified
  iv_with_img = abap_true ).
```

Result:

```html
    <p>First paragraph.</p>
    <ul>
        <li>Item A</li>
        <li>Item B</li>
        <li>Item <del>C</del><ins>D</ins></li>
    </ul>
    <del><img src='previous.jpg'></del><ins><img src='next.jpg'></ins>
    <span>This is some <del>interesting</del><ins>new</ins> text.</span>
```

There are a few other options you can set in the `constructor`/`create` statement:

- `iv_inserts`: Show `<ins>` tags (default: on)
- `iv_deletes`: Show `<del>` tags (default: on)
- `iv_css_classes`: Add CSS classes to `<ins>` and `<del>` tags (default: off)
- `iv_support_chinese`: Treat Chinese characters as individual words (default: off)

Using CSS classes, the result will distinguish between inserts (class `diffins`), deletes (class `diffdel`), and updates (class `diffmod`).

See the [test classes](https://github.com/abapPM/ABAP-HTML-Diff/blob/main/src/zcl_htmldiff.clas.testclasses.abap) for more examples.

### Diffing Text

The following produces the diff of two example text snippets:

```abap
DATA:
  lv_original TYPE string,
  lv_modified TYPE string,
  lv_diff     TYPE string,
  li_htmldiff TYPE REF TO zif_htmldiff.

lv_original = 'a c'.

lv_modified = 'a b c'.

lv_diff = li_htmldiff->textdiff(
  iv_before = lv_original
  iv_after  = lv_modified ).
```

Result:

```text
a <ins>b </ins>c
```

Example 2:

```abap
lv_original = 'a b c'.

lv_modified = 'a c'.

lv_diff = li_htmldiff->textdiff(
  iv_before = lv_original
  iv_after  = lv_modified ).
```

Result:

```text
a <del>b </del>c
```

### Styling

Here's an examle for styling the insertions and deletions using CSS.

```css
/* CSS for <ins> and <del> tags */
ins { background-color: #ddffdd; }
ins img { border-color: #ddffdd; }

del { background-color: #ffdddd; }
del img { border-color: #ffdddd; }
```

With the CSS class option, use the following:

```css
/* CSS for insert, delete, and modify classes */
.diffins { background-color: #ddffdd; }
.diffdel { background-color: #ffdddd; }
.diffmod { background-color: #ffffdd; }
```

## Installation

Install `html-diff` as a global module in your system using [apm](https://abappm.com).

or

Specify the `html-diff` module as a dependency in your project and import it to your namespace using [apm](https://abappm.com).

## Contributions

All contributions are welcome! Read our [Contribution Guidelines](https://github.com/abapPM/ABAP-HTML-Diff/blob/main/CONTRIBUTING.md), fork this repo, and create a pull request.

You can install the developer version of ABAP HTML Diff using [abapGit](https://github.com/abapGit/abapGit) by creating a new online repository for `https://github.com/abapPM/ABAP-HTML-Diff`.

Recommended SAP package: `$HTML-DIFF`

## Attribution

- The implementation is a port of JavaScript (https://github.com/alaorneto/htmldiffer, no license defined)
- which is a port of CoffeeScript (https://github.com/tnwinc/htmldiff.js, MIT license)
- which is a port of the original Ruby (https://github.com/myobie/htmldiff, MIT license)

## About

Made with ❤ in Canada

Copyright 2025 apm.to Inc. <https://apm.to>

Follow [@marcf.be](https://bsky.app/profile/marcf.be) on Bluesky and [@marcfbe](https://linkedin.com/in/marcfbe) or LinkedIn

