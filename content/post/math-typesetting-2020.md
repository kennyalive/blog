---
title: "Math Typesetting Test: Macrosurface BSDF Derivation"
date: 2020-01-08T11:54:23+01:00
draft: true
markup: mmark
enable_math: true
---

Some time ago I made a derivation of the known formula from the microfacet theory that shows how the reflection/refraction properties of the surface (macrosurface BSDF) depend on the reflection/refraction properties of the small facets that form the surface (microsurface BSDF). That work was done on paper. Now I decided to learn how to typeset math formulas in a digital form. I used this page as a playground to accomplish this goal and also to repeat the derivation.

As of 2020 the \\(\TeX\\) typesetting language is quite popular. It provides a markup language rich enough to write even the complex multi-volume book but we are mostly interested in a math subset of \\(\TeX\\). There are few options how to expose it on the web. \\(\KaTeX\\) JavaScript library is a solution that is used here. Another good option is the *MathJax* library.

At first, I will describe the software configuration that makes my site math-friendly. Then, a few examples of the \\(\TeX\\) math notation will be provided. Finally, we will play with basic radiometry definitions to get the desired relationship.

### Software configuration

This site is an output of *Hugo site generator*. Hugo is a program that consumes content in the form of *Markdown-formatted documents* and produces a web site - a collection of the files (HTML, CSS, JavaScript, images, etc.) that can be served by a web server. The site's layout and styles are defined by a *Hugo theme*. There are a lot of free themes available online.

*KaTeX JavaScript library*. It renders mathematical notations. [KaTeX  Auto-render Extension](https://katex.org/docs/autorender.html) page provides html snippet that should be inserted into html header:
```xml
  <!-- the head tag is usually defined by the theme, for example,
  in the theme that I use it's in the layouts/partials/header.html file -->
  <head>
  .....
  {{ with .Params.enable_math}}
  <!-- Put here html snippet from autorender documentation page -->
  {{ end }}
  </head>
```

*Content metadata*. Markdown document processed by Hugo should begin with a front matter section that defines a metadata associated with the document. We need to add the following two properties to enable usage of the KaTex library:
* `markup` - allows to set non-default markdown engine. We need this because the default engine does not support KaTeX. <sup>[1](#footnote1)</sup>
* `enable_math` - our custom property which enables KaTeX library on the given page (by default it is disabled)

```
---
title: "Math typesetting 2020"
date: 2020-01-08T11:54:23+01:00
markup: mmark
enable_math: true
---
```

### Math notation
\\(\KaTeX\\) determines which parts of the document contain math by looking for the delimiters that surround the math snippets. There are two math rendering modes, each one is defined by a different pair of delimiters.
* *Inline mode* delimiters are `\\(` and `\\)`. In inline mode the math formula  won't break the line, for example, here is the assigment \\(a=b+c\\) in the middle of this line produced by `\\(a=b+c\\)`

* *Displayed mode* delimiters are `\\[` and `\\]` <sup>[2](#footnote2)</sup>. In displayed mode the formula adds a line break. Here is the output of `\\[a=b+c\\]` markup: \\[a = b + c\\]

As shown above, basic arithmetic operations have a natural syntax. Here is a list of additional operations that I used in BSDF derivation:

`\int`\\(\dashrightarrow \int\\) \\
`\underset{X}{\int}` \\(\dashrightarrow \underset{X}{\int}\\) \\
`a \cdot b`  \\(\dashrightarrow a \cdot b \\) \\
`x^2` \\(\dashrightarrow x^2 \\) \\
`x_i` \\(\dashrightarrow x_i \\) \\
`x_i^2` \\(\dashrightarrow x_i^2\\) \\
`\frac{a}{b}` \\(\dashrightarrow \frac{a}{b}\\) \\
`\Phi` \\(\dashrightarrow \Phi\\) \\
`\bold a` \\(\dashrightarrow \bold a\\) \\
`\bigg|` \\(\dashrightarrow \bigg| \\)

The full list of supported \\(\TeX\\) functions can be found in [KaTeX Documentation](https://katex.org/docs/supported.html)

### Macrosurface BSDF integral derivation
In the well known computer graphics paper *"Microfacet Models for Refraction through Rough Surfaces"* (Walter et al. 2007) there is a formula that shows relationship between microsurface and macrosurface BSDFs:

\\[ \tag{1} f_s(\bold i, \bold o, \bold n) = 
   \underset{H^2(\bold n)}{\int} \bigg| \frac{\bold i \cdot \bold m }{\bold i \cdot \bold n} \bigg|
   f_s^m(\bold i, \bold o, \bold m)
   \bigg| \frac{\bold o \cdot \bold m }{\bold o \cdot \bold n} \bigg|
   G(\bold i, \bold o, \bold m) D(\bold m) d\omega_m \\]

Where \\(f_s\\) and \\(f_s^m\\) are the macrosurface and the microsurface BSDFs correspondingly, \\(G\\) and \\(D\\) are the shadowing-masking and the normal distribution functions as specified in the microfacet model, \\(\bold i\\) is the incident light direction, \\(\bold o\\) is the outgoing light direction, \\(\bold n\\) is the macrosurface normal and \\(\bold m\\) is the microsurface normal. \\(H^2(\bold n)\\) denotes the hemisphere of directions about a given surface normal \\(\bold n\\).

![microsurface](/math-test/microsurface.png#center)

We can obtain formula \\((1)\\) by computing the *radiant flux* due to reflected or refracted light. Radiant flux is how the *power* is called in radiometry, i.e. the amount of energy the light carries through the region of space per unit time. We are going to compute the flux twice. At first by using the macrosurface BSDF and the second time the same quantity will be computed based on the microsurface BSDF. Then we can get the equation \\((1)\\) by equating both results. The wavelength dependency of radiometric quantities is ommited for simplicity. In the calculations that follow we observe the incoming and outgoing light in a narrow set a directions defined by the differential solid angles \\(d\omega_i\\) and \\(d\omega_o\\) correspondingly, which also define directions \\(\bold i\\) and \\(\bold o\\).

###### a) Flux computation according to macrosurface BSDF $$f_s$$

![reflection_macro](/math-test/reflection_macro.png#center)

Let's start by calculating irradiance due to the light from solid angle \\(d\omega_i\\):
\\[ dE =L_i d\omega_i \lvert(\bold n \cdot \bold i)\rvert \\]

The amount of irradiance \\(dE\\) creates distribution of reflected and refracted light according to BSDF, specificially the outgoing radiance in the direction \\(\bold o \\) is:
\\[ dL_o = f_s(\bold i, \bold o, \bold n) dE \\]

Having outgoing radiance we can compute the radiance exitance (another name with a nice retro tint is \\(\mathcal{radiosity}\\)) considering outgoing light in a solid angle \\(d\omega_o\\):
\\[ dM = dL_o d\omega_o \lvert(\bold n \cdot \bold o)\rvert \\]

Outgoing flux from differential surface area \\(dA\\):
\\[ \tag{2} d\Phi = dMdA = f_s(\bold i, \bold o, \bold n)L_i
   \lvert(\bold n \cdot \bold i)
   (\bold n \cdot \bold o)\rvert
   d\omega_i d\omega_o dA \\]

###### b) Flux computation according to microsurface BSDF $$f_s^m$$

![reflection_micro](/math-test/reflection_micro.png#center)
Surface with microgeometry is a collection of facets of different orientation. At first we will compute outgoing flux from facets that have the same orientation \\(\bold m\\). Then the total flux is an integral over all possible facet orientations. All quantities that are computed for a subset of facets with normal \\(\bold m \\) are provided with corresponding subscript.

Irradiance, outgoing radiance and \\(\mathcal{radiosity}\\) are computed identically to macrosurface case with a difference that we use facet's normal \\(\bold m\\) instead of \\(\bold n \\) and microsurface BSDF \\(f_s^m\\) instead of \\(f_s\\):
\\[ dE_m = L_i d\omega_i \lvert(\bold m \cdot \bold i)\rvert \\]

$$ dL_{mo} = f_s^m(\bold i, \bold o, \bold m) dE_m $$

$$ dM_m = dL_{mo} d\omega_o \lvert(\bold m \cdot \bold o)\rvert $$

The total area of facets with normal \\(\bold m \\) that receive light and reflect it without self-shadowing is computed according to definitions of functions \\(D\\) and \\(G\\):
\\[ dA_m = D G d\omega_m dA \\]

where \\(d\omega_m\\) is the differential solid angle oriented along \\(\bold m\\). Outgoing flux from facets with normal \\(\bold m\\):
\\[ d\Phi_m = dM_mdA_m \\]

Total flux is computed by considering all facets (integration domain is a hemisphere around macrosurface normal \\(\bold n\\)):
\\[ \tag{3} d\Phi = \underset{H^2(\bold n)}{\int} d\Phi_m d\omega_m =
   \underset{H^2(\bold n)}{\int} f_s^m(\bold i, \bold o, \bold m) L_i
   \lvert(\bold m \cdot \bold i)
   (\bold m \cdot \bold o)\rvert
   D G
   d\omega_i d\omega_o dA d\omega_m
\\]

###### c) Getting the result

By equating \\((2)\\) and \\((3)\\) and noticing that \\(d\omega_i\\), \\(d\omega_o\\), \\(dA\\) and \\(L_i\\) do not depend on the integration domain we get the desired result:

\\[ f_s(\bold i, \bold o, \bold n)L_i
   \lvert(\bold n \cdot \bold i)
   (\bold n \cdot \bold o)\rvert
   d\omega_i d\omega_o dA = 
   \underset{H^2(\bold n)}{\int} f_s^m(\bold i, \bold o, \bold m) L_i
   \lvert(\bold m \cdot \bold i)
   (\bold m \cdot \bold o)\rvert
   D G
   d\omega_i d\omega_o dA d\omega_m
\\]

\\[\Downarrow\\]

\\[ f_s(\bold i, \bold o, \bold n) = 
   \underset{H^2(\bold n)}{\int} \bigg| \frac{\bold i \cdot \bold m }{\bold i \cdot \bold n} \bigg|
   f_s^m(\bold i, \bold o, \bold m)
   \bigg| \frac{\bold o \cdot \bold m }{\bold o \cdot \bold n} \bigg|
   G(\bold i, \bold o, \bold m) D(\bold m) d\omega_m \\]

That's the end of the derivation.

By using specific microsurface BSDFs we can derive BSDF expressions that are commonly used in computer graphics. For example, by assuming that microfacets are optically flat surfaces with \\(f_s^m\\) of a mirror the expression \\((1)\\) is simplified to this form:

\\[ f_r(\bold i, \bold o, \bold n) = \frac
   {F(\bold i, \bold h_r) G(\bold i, \bold o, \bold h_r) D(\bold h_r)}
   {4\lvert|\bold i \cdot \bold n\rvert \lvert|\bold o \cdot \bold n\rvert}
 \\]

<a name="footnote1">1</a>: Here should be information about mmark support

<a name="footnote2">2</a>: Write here about `$$` delimiter