---
title: "Math Typesetting Test: Macrosurface BSDF Derivation"
date: 2020-02-13T00:28:17+01:00
markup: mmark
enable_math: true
---

Some time ago, I made a derivation of the known formula from the microfacet theory that shows how the reflectance properties of the surface depend on the reflectance properties of the small facets that form the surface. That work was done on paper. Now I decided to learn how to typeset math formulas in a digital form<sup>[0](#footnote0)</sup>. This post will repeat the derivation with the help of contemporary software, where some parts of the technology can be traced back to the late '70s of the previous century.

As of 2020, the \\(\TeX\\) typesetting language is quite popular. It provides a markup language rich enough to write even the complex multi-volume book, but we are mostly interested in a math subset of \\(\TeX\\). There are few options on how to expose it on the web. I used \\(\KaTeX\\) JavaScript library. Another good option is the *MathJax* library.

At first, I will describe the software configuration that makes my site math-friendly. Then, I will provide a few examples of the \\(\TeX\\) math notation. The last part is computer graphics related, where we will play with radiometric quantities to get the desired relationship.

### Software configuration

This site (including this page) is an output of ***Hugo site generator***. Hugo is a program that consumes content in the form of *Markdown-formatted documents* and produces a web site - a collection of the files (HTML, CSS, JavaScript, images, etc.) that can be served by a web server. The site's layout and styles are defined by a *Hugo theme*. There are a lot of free themes available online.

***KaTeX JavaScript library*** is used to render mathematical formulas. [KaTeX  Auto-render Extension](https://katex.org/docs/autorender.html) provides html snippet that should be inserted into html header to enable KaTeX on the web page:
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

***Content metadata***. The markdown documents consumed by Hugo should begin with a front matter section that defines metadata associated with a document. I had to add the following two properties to enable usage of the KaTex library:
* `markup` - allows to set non-default markdown engine. We need this because the default engine does not support KaTeX yet. <sup>[1](#footnote1)</sup>
* `enable_math` - a custom property which enables KaTeX library on the current page (by default math is disabled)

```toml
  ---
  title: "Math Typesetting Test: Macrosurface BSDF Derivation"
  date: 2020-02-13T00:28:17+01:00
  markup: mmark
  enable_math: true
  ---
```

### Math notation
\\(\KaTeX\\) determines which parts of the document contain math by looking for the delimiters that surround the math snippets. There are two math rendering modes, and each one is defined by a different pair of delimiters.
* *Inline mode* delimiters: `\\(` and `\\)`. In inline mode, the math formula  won't break the line, for example, here is the assignment \\(a=b+c\\) in the middle of this line produced by `\\(a=b+c\\)`

* *Display mode* delimiters: `\\[` and `\\]` <sup>[2](#footnote2)</sup>. In display mode the formula adds a line break. Here is the output of `\\[a=b+c\\]` markup: \\[a = b + c\\]

As shown above, basic arithmetic actions have a natural syntax. Here is a list of additional operations that I used in BSDF derivation:

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
According to the microfacet model, the surface has a microstructure (black outline) that determines the surface's reflectance properties. Those details are small, and for an ordinary person, the surface still looks nice and smooth (green outline) :
![microsurface](/math-test/microsurface.png#center)

In the well known-computer graphics paper *"Microfacet Models for Refraction through Rough Surfaces"* (Walter et al. 2007) there is a formula that shows the relationship between microsurface and macrosurface BSDFs:
<div class="formula">
\\[ \tag{1} f_s(\bold i, \bold o, \bold n) = 
   \underset{H^2(\bold n)}{\int} \bigg| \frac{\bold i \cdot \bold m }{\bold i \cdot \bold n} \bigg|
   f_s^m(\bold i, \bold o, \bold m)
   \bigg| \frac{\bold o \cdot \bold m }{\bold o \cdot \bold n} \bigg|
   G(\bold i, \bold o, \bold m) D(\bold m) d\omega_m
\\]
</div>

The symbols have the following meaning:
* \\(f_s\\) and \\(f_s^m\\) are the macrosurface and the microsurface BSDFs
* \\(G\\) and \\(D\\) are the shadowing-masking and the normal distribution functions from the microfacet theory
* \\(\bold i\\) and \\(\bold o\\) are the incident and outgoing light directions
* \\(\bold n\\)  and \\(\bold m\\) are the macrosurface and the microsurface normals
* \\(H^2(\bold n)\\) denotes the hemisphere of directions above the given surface normal \\(\bold n\\).

The above equation may look a bit academic, and you won't meet it too often in public places in contrast to this guy:

\\[ \tag{shiny} f_r(\bold i, \bold o, \bold n) = \frac
   {F(\bold i, \bold h_r) G(\bold i, \bold o, \bold h_r) D(\bold h_r)}
   {4\lvert|\bold i \cdot \bold n\rvert \lvert|\bold o \cdot \bold n\rvert}
 \\]

 But! The truth is that \\((shiny)\\) and \\((1)\\) are in close relation with one another. You can get \\((shiny)\\) if you put \\(f_s^m\\) = Mirror_BRDF into \\((1)\\). Even more, you can put \\((shiny)\\) itself into \\((1)\\) but it's hard to imagine what happens in that case <sup>[3](#footnote3)</sup>. Another bold idea is to assume that \\(f_s^m\\) is a Lambertian reflector, but that's starting to look like a rocket (moon?) science... The point is that \\((1)\\) is an important equation, and it could be an interesting exercise to get it from the basic principles.

We can obtain formula \\((1)\\) by computing the *radiant flux* due to reflected or refracted light. The radiant flux is how the *power* is called in radiometry, i.e., the amount of energy the light carries through the region of space per unit time. We are going to compute the same flux quantity in two different ways, by using macrosurface and microsurface representations correspondingly. Then we can get \\((1)\\) by equating both results.

The wavelength dependency of radiometric quantities is omitted for simplicity. In the calculations that follow we observe the incoming and outgoing light in a narrow set a directions defined by the differential solid angles \\(d\omega_i\\) and \\(d\omega_o\\) correspondingly, which also define directions \\(\bold i\\) and \\(\bold o\\).

Also, the equations below are mostly basic radiometric definitions. Some excellent resources that introduce these concepts are listed at the bottom of this page <sup>[4](#footnote4)</sup>

###### a) Flux computation according to macrosurface BSDF \\(f_s\\)

![reflection_macro](/math-test/reflection_macro.png#center)

Let's start by calculating irradiance due to the light from a solid angle \\(d\omega_i\\):
\\[ dE =L_i d\omega_i \lvert(\bold n \cdot \bold i)\rvert \\]

The amount of irradiance \\(dE\\) creates a distribution of reflected and refracted light according to BSDF, specifically the outgoing radiance in the direction \\(\bold o \\) is:
\\[ dL_o = f_s(\bold i, \bold o, \bold n) dE \\]

Having outgoing radiance we can compute the radiance exitance (another name with a nice retro tint is \\(\mathcal{radiosity}\\)) considering outgoing light in a solid angle \\(d\omega_o\\):
\\[ dM = dL_o d\omega_o \lvert(\bold n \cdot \bold o)\rvert \\]

Outgoing flux from differential surface area \\(dA\\):
\\[ \tag{2} d\Phi = dMdA = f_s(\bold i, \bold o, \bold n)L_i
   \lvert(\bold n \cdot \bold i)\rvert
   \lvert(\bold n \cdot \bold o)\rvert
   d\omega_i d\omega_o dA \\]

###### b) Flux computation according to microsurface BSDF \\(f_s^m\\)

![reflection_micro](/math-test/reflection_micro.png#center)
The surface can be viewed as a collection of facets of different orientation. At first we will compute outgoing flux from the facets that have the same orientation \\(\bold m\\) (to be more precise there are zero facets with some specific orientation and we implicitly assume some infinitesimal deviation \\(\pm d \bold m\\) is okay). Then the total flux is an integral over all possible facet orientations. All quantities that are computed for a subset of facets with normal \\(\bold m \\) are provided with corresponding subscript.

Irradiance, outgoing radiance and \\(\mathcal{radiosity}\\) are computed identically to macrosurface case with a difference that we use facet's normal \\(\bold m\\) instead of \\(\bold n \\) and microsurface BSDF \\(f_s^m\\) instead of \\(f_s\\):
\\[ dE_m = L_i d\omega_i \lvert(\bold m \cdot \bold i)\rvert \\]

\\[ dL_{mo} = f_s^m(\bold i, \bold o, \bold m) dE_m \\]

$$ dM_m = dL_{mo} d\omega_o \lvert(\bold m \cdot \bold o)\rvert $$

The total area of the facets with normal \\(\bold m \\) that receive light and reflect it without self-shadowing is computed according to how \\(D\\) and \\(G\\) are defined:
\\[ dA_m = D G d\omega_m dA \\]

where \\(d\omega_m\\) is the differential solid angle oriented along \\(\bold m\\). Outgoing flux from facets with normal \\(\bold m\\):
\\[ d\Phi_m = dM_mdA_m \\]

Total flux is computed by considering all facets (integration domain is a hemisphere around macrosurface normal \\(\bold n\\)):
\\[ \tag{3} d\Phi = \underset{H^2(\bold n)}{\int} d\Phi_m d\omega_m
   \newline
   = \underset{H^2(\bold n)}{\int} f_s^m(\bold i, \bold o, \bold m) L_i
   \lvert(\bold m \cdot \bold i)\rvert
   \lvert(\bold m \cdot \bold o)\rvert
   D G
   d\omega_i d\omega_o dA d\omega_m \\]

###### c) Getting the result

By equating \\((2)\\) and \\((3)\\) and noticing that \\(d\omega_i\\), \\(d\omega_o\\), \\(dA\\) and \\(L_i\\) do not depend on the integration domain we get the desired result:
<div class="formula">
\\[ f_s(\bold i, \bold o, \bold n)L_i
   \lvert(\bold n \cdot \bold i)\rvert
   \lvert(\bold n \cdot \bold o)\rvert
   d\omega_i d\omega_o dA = 
   \underset{H^2(\bold n)}{\int} f_s^m(\bold i, \bold o, \bold m) L_i
   \lvert(\bold m \cdot \bold i)\rvert
   \lvert(\bold m \cdot \bold o)\rvert
   D G
   d\omega_i d\omega_o dA d\omega_m
\\]
</div>
\\[\Downarrow\\]
<div class="formula">
\\[ f_s(\bold i, \bold o, \bold n) = 
   \underset{H^2(\bold n)}{\int} \bigg| \frac{\bold i \cdot \bold m }{\bold i \cdot \bold n} \bigg|
   f_s^m(\bold i, \bold o, \bold m)
   \bigg| \frac{\bold o \cdot \bold m }{\bold o \cdot \bold n} \bigg|
   G(\bold i, \bold o, \bold m) D(\bold m) d\omega_m
\\]
</div>

![wink](/math-test/wink.png#center)

### Notes
<a name="footnote0">0</a>. Matt Pettineo's [Hello, Hugo](https://therealmjp.github.io/posts/hello-hugo/) post where he confirmed that it's possible to use math with Hugo, helped with a decision to start revamping my own site.

<a name="footnote1">1</a>. There are ongoing developments to add KaTeX support to Hugo's default markdown engine (goldmark). I'll update this post to use default engine when that functionality is available.

<a name="footnote2">2</a>. KaTeX allows to use `$$`, `$$` pair as an alternative to `\\[`, `\\]`. I had to use $$ delimiter once in this post because `\\[`, `\\]` pair did not work for unknown reason.

<a name="footnote3">3</a>. I guess Eric Heitz who wrote [impressive paper about scattering on microgeometry](http://jcgt.org/published/0003/02/03/paper.pdf) might know the answer.

<a name="footnote4">4</a>. Radiometric quantities in computer graphics:
* Awesome and free **[PBRT book](http://www.pbr-book.org/3ed-2018/Color_and_Radiometry/Radiometry.html)**, 3rd edition, section 5.4.
* *Advanced Global Illumination* by Philip Dutre et al., 2nd edition, section 2.3. The first five chapters of this book is probably my favourite introduction to light transport theory.
* [Veach's thesis] (https://graphics.stanford.edu/papers/veach_thesis/thesis-bw.pdf), sections 3.4-3.5.

Attribution: the free wink icon is from `freepngimg.com` site.
