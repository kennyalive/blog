---
title: "Math Typesetting 2020"
date: 2020-02-13T00:28:17+01:00
enable_math: true
---

As of 2020, the \\(\TeX\\) typesetting language is quite popular. It provides a markup language rich enough to write even a complex multi-volume book. Still, I'm primarily interested in a math subset of \\(\TeX\\). There are a few options for how to expose it on the web. I used the \\(\KaTeX\\) JavaScript library. Another option is the *MathJax* library.

This post will describe the software configuration that makes my site math-friendly. I will also provide a few examples of the \\(\TeX\\) math notation.

### Software configuration

This site (including this page) is an output of ***Hugo site generator***. Hugo is a program that consumes content in the form of *Markdown-formatted documents* and produces a web site - a collection of the files (HTML, CSS, JavaScript, images, etc.) that can be served by a web server. The site's layout and styles are defined by a *Hugo theme*. There are a lot of free themes available online.

***KaTeX JavaScript library*** is used to render mathematical formulas. [KaTeX  Auto-render Extension](https://katex.org/docs/autorender.html) provides html snippet that should be inserted into html header to enable KaTeX on the web page:
```xml
  <!-- The head tag is usually defined by the theme.
  For the theme that I use it's in the layouts/partials/header.html -->
  <head>
  .....
  <!-- Enable KaTeX math library if the page needs it -->
  {{ with .Params.enable_math}}
    <!-- Put here html snippet from Auto-render Extension page -->
  {{ end }}
  </head>
```

***Content metadata***. The markdown documents consumed by Hugo should begin with a front matter section that defines metadata associated with a document. I added a custom property to the front matter section:
* `enable_math` - enables KaTeX library on the current page (by default math is disabled)

```toml
  ---
  title: "Math Typesetting 2020"
  date: 2020-02-13T00:28:17+01:00
  enable_math: true
  ---
```

### Math notation
\\(\KaTeX\\) determines which parts of the document contain math by looking for the delimiters that surround the math snippets. There are two rendering modes; each one is defined by a different pair of delimiters.
* *Inline mode* delimiters: `\\(` and `\\)`. In inline mode, a math formula  does not break the line. Here is the assignment \\(a=b+c\\) in the middle of this line produced by `\\(a=b+c\\)`

* *Display mode* delimiters: `\\[` and `\\]`. In display mode, the formula adds a line break. Here is the output of `\\[a=b+c\\]` markup: \\[a = b + c\\]

As shown above, basic arithmetic operations have a natural syntax. Here are few more examples:

`\int`\\(\dashrightarrow \int\\)

`\underset{X}{\int}` \\(\dashrightarrow \underset{X}{\int}\\)

`a \cdot b`  \\(\dashrightarrow a \cdot b \\)

`x^2` \\(\dashrightarrow x^2 \\)

`x_i` \\(\dashrightarrow x_i \\)

`x_i^2` \\(\dashrightarrow x_i^2\\)

`\frac{a}{b}` \\(\dashrightarrow \frac{a}{b}\\)

`\Phi` \\(\dashrightarrow \Phi\\)

`\bold a` \\(\dashrightarrow \bold a\\)

`\bigg|` \\(\dashrightarrow \bigg| \\)

The full list of supported \\(\TeX\\) functions can be found in [KaTeX Documentation](https://katex.org/docs/supported.html)
