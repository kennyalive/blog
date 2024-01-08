---
title: "Macrosurface BSDF Derivation"
date: 2020-02-13T00:28:17+01:00
enable_math: true
---

Some time ago, I made a derivation of the known formula from microfacet theory that shows how the reflectance properties of the surface depend on the reflectance properties of the facets that form the surface. That work was done on paper. Now, I decided to learn how to typeset math formulas in a digital form. This post will repeat the derivation. We will play with radiometric quantities to get the desired relationship.

### Macrosurface BSDF integral derivation
According to the microfacet model, the surface has a microstructure (black outline) that determines surface reflectance properties. Those details are small, and for an ordinary person, the surface still looks nice and smooth (green outline):
![microsurface](/math-test/microsurface.png#center)

In the well-known computer graphics paper *"Microfacet Models for Refraction through Rough Surfaces"* (Walter et al. 2007) there is a formula that shows the relationship between microsurface and macrosurface BSDFs:
\\[ \tag{1} f_s(\bold i, \bold o, \bold n) = 
   \underset{H^2(\bold n)}{\int} \bigg| \frac{\bold i \cdot \bold m }{\bold i \cdot \bold n} \bigg|
   f_s^m(\bold i, \bold o, \bold m)
   \bigg| \frac{\bold o \cdot \bold m }{\bold o \cdot \bold n} \bigg|
   G(\bold i, \bold o, \bold m) D(\bold m) d\omega_m
\\]

The symbols have the following meaning:
* \\(f_s\\) and \\(f_s^m\\) are the macrosurface and the microsurface BSDFs
* \\(G\\) and \\(D\\) are the shadowing-masking and the normal distribution functions from the microfacet theory
* \\(\bold i\\) and \\(\bold o\\) are the incident and outgoing light directions
* \\(\bold n\\)  and \\(\bold m\\) are the macrosurface and the microsurface normals
* \\(H^2(\bold n)\\) denotes the hemisphere of directions above the given surface normal \\(\bold n\\).

The above equation may look a bit academic, and you don't meet it too often comparing to this guy:

\\[ \tag{shiny} f_r(\bold i, \bold o, \bold n) = \frac
   {F(\bold i, \bold h_r) G(\bold i, \bold o, \bold h_r) D(\bold h_r)}
   {4\lvert|\bold i \cdot \bold n\rvert \lvert|\bold o \cdot \bold n\rvert}
 \\]

 It turns out that \\((shiny)\\) and \\((1)\\) are closely related. You can get \\((shiny)\\) if you put \\(f_s^m\\) = Mirror_BRDF into \\((1)\\). Another idea is to use specular transmission brdf to derive macro brdf of the rough transmissive surface. The point is that \\((1)\\) is an important equation, and it could be an interesting exercise to get it from the basic principles.

We can obtain formula \\((1)\\) by computing the *radiant flux* due to reflected or refracted light. The radiant flux is what the *power* is called in radiometry, i.e., the amount of energy the light carries through the region of space per unit time. We will compute the same flux quantity in two different ways by using macrosurface and microsurface representations correspondingly. Then, we can get \\((1)\\) by equating both results.

The wavelength dependency of radiometric quantities is omitted for simplicity. In the calculations that follow, we observe the incoming and outgoing light in a narrow set of directions defined by the differential solid angles \\(d\omega_i\\) and \\(d\omega_o\\) correspondingly, which also define directions \\(\bold i\\) and \\(\bold o\\).

Also, the equations below are mostly basic radiometric definitions. Some excellent resources that introduce these concepts are listed at the end of this post. [^1]

###### a) Flux computation according to macrosurface BSDF \\(f_s\\)

![reflection_macro](/math-test/reflection_macro.png#center)

Let's start by calculating irradiance due to the light from a solid angle \\(d\omega_i\\):
\\[ dE =L_i d\omega_i \lvert(\bold n \cdot \bold i)\rvert \\]

The amount of irradiance \\(dE\\) creates a distribution of reflected and refracted light according to BSDF. Specifically, the outgoing radiance in the direction \\(\bold o \\) is:
\\[ dL_o = f_s(\bold i, \bold o, \bold n) dE \\]

Now we can compute radiance exitance (alternative name with a retro tint is \\(\mathcal{radiosity}\\)) considering outgoing light in a solid angle \\(d\omega_o\\):
\\[ dM = dL_o d\omega_o \lvert(\bold n \cdot \bold o)\rvert \\]

Outgoing flux from the differential surface area \\(dA\\):
\\[ \tag{2} d\Phi = dMdA = f_s(\bold i, \bold o, \bold n)L_i
   \lvert(\bold n \cdot \bold i)\rvert
   \lvert(\bold n \cdot \bold o)\rvert
   d\omega_i d\omega_o dA \\]

###### b) Flux computation according to microsurface BSDF \\(f_s^m\\)

![reflection_micro](/math-test/reflection_micro.png#center)
The surface can be viewed as a collection of facets of different orientations. At first, we will compute outgoing flux from the facets with the same orientation (orientation is defined by a differential solid angle). The total flux is an integral over all possible facet orientations. The quantities computed for a subset of facets with normal \\(\bold m \\) are provided with the *m* subscript.

Irradiance, outgoing radiance and \\(\mathcal{radiosity}\\) are computed identically to macrosurface case with a difference that we use facet's normal \\(\bold m\\) instead of \\(\bold n \\) and microsurface BSDF \\(f_s^m\\) instead of \\(f_s\\):
\\[ dE_m = L_i d\omega_i \lvert(\bold m \cdot \bold i)\rvert \\]

\\[ dL_{mo} = f_s^m(\bold i, \bold o, \bold m) dE_m \\]

$$ dM_m = dL_{mo} d\omega_o \lvert(\bold m \cdot \bold o)\rvert $$

The total area of the facets with normal \\(\bold m \\) that receive light and reflect it without self-shadowing is computed according to how \\(D\\) and \\(G\\) are defined:
\\[ dA_m = D G d\omega_m dA \\]

where \\(d\omega_m\\) is the differential solid angle oriented along \\(\bold m\\). Outgoing flux from facets with normal \\(\bold m\\):
\\[ d\Phi_m = dM_mdA_m \\]

Total flux is computed by considering all facets (integration domain is a hemisphere around macrosurface normal \\(\bold n\\)):
\\[ d\Phi = \underset{H^2(\bold n)}{\int} d\Phi_m d\omega_m \\]
\\[ \tag{3}  = \underset{H^2(\bold n)}{\int} f_s^m(\bold i, \bold o, \bold m) L_i
   \lvert(\bold m \cdot \bold i)\rvert
   \lvert(\bold m \cdot \bold o)\rvert
   D G
   d\omega_i d\omega_o dA d\omega_m \\]

###### c) Getting the result

By equating \\((2)\\) and \\((3)\\) and noticing that \\(d\omega_i\\), \\(d\omega_o\\), \\(dA\\) and \\(L_i\\) do not depend on the integration domain we get the desired result:
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
\\[\Downarrow\\]
\\[ f_s(\bold i, \bold o, \bold n) = 
   \underset{H^2(\bold n)}{\int} \bigg| \frac{\bold i \cdot \bold m }{\bold i \cdot \bold n} \bigg|
   f_s^m(\bold i, \bold o, \bold m)
   \bigg| \frac{\bold o \cdot \bold m }{\bold o \cdot \bold n} \bigg|
   G(\bold i, \bold o, \bold m) D(\bold m) d\omega_m
\\]

![wink](/math-test/wink.png#center)

Attribution: the wink icon is from `freepngimg.com`

[^1]: Radiometric quantities in computer graphics:
    * Awesome and free **[PBRT book](http://www.pbr-book.org/3ed-2018/Color_and_Radiometry/Radiometry.html)**, 3rd edition, section 5.4.
    * *Advanced Global Illumination* by Philip Dutre et al., 2nd edition, section 2.3. The first five chapters of this book are probably my favorite introduction to light transport theory.
    * [Veach's thesis](https://graphics.stanford.edu/papers/veach_thesis/thesis-bw.pdf), sections 3.4-3.5.


