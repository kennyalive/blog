---
title: "Microsurface Refraction BSDF Derivation"
date: 2024-05-21T23:13:12+02:00
enable_math: true
---

In the microfacet model, the surface BSDF is expressed through the BSDF of separate facets as follows:

\\[ \tag{1} f_s(\bold i, \bold o, \bold n) = 
   \underset{H^2(\bold n)}{\int} \bigg| \frac{\bold i \cdot \bold m }{\bold i \cdot \bold n} \bigg|
   f_s^m(\bold i, \bold o, \bold m)
   \bigg| \frac{\bold o \cdot \bold m }{\bold o \cdot \bold n} \bigg|
   G(\bold i, \bold o, \bold m) D(\bold m) d\omega_m
\\]

There is a derivation [here](/post/macrosurface-bsdf/).

This article is about getting expression for the microsurface BSDF \\(f_s^m(\bold i, \bold o, \bold m)\\) in the case of ideal specular *refraction*.

### Warmup

Firstly, here is an expression for the case of ideal specular *reflection*:

\\[ \tag{2} f_s^m(\bold i, \bold o, \bold m) =
   F(\bold i, \bold m) \frac{\delta_{\omega_i}(\bold i - reflect(\bold o, \bold m))}{\lvert \bold i \cdot \bold m \rvert}
\\]

The Dirac delta function is used to reflect light in a single direction. The Fresnel term reflects only a portion of the light, based on Fresnel physics. The \\(\lvert \bold i \cdot \bold m \rvert \\) in the denominator cancels out the same term when we integrate over all incident directions.

Since equation \\((1)\\) integrates over microsurface normal directions, but the delta function \\(\delta_{\omega_i}\\) uses the measure related to the incident direction, we need to rewrite \\((2)\\) in the form that uses a solid angle measure related to the microsurface normal - \\(\delta_{\omega_m}\\). Then it can be injected into the \\((1)\\).

Updating the measure introduces an additional Jacobian term \\(\frac{\partial \omega_h}{\partial \omega_i}\\). The microsurface specular *reflection* BSDF can be written as follows:

\\[ \tag{3} f_s^m(\bold i, \bold o, \bold m) =
   F(\bold i, \bold m) \frac{\delta_{\omega_m}(\bold m - \bold h(\bold i, \bold o))}{\lvert \bold i \cdot \bold m \rvert}
   \bigg| \frac{\partial \omega_h}{\partial \omega_i} \bigg|
\\]

### Microsurface refraction BSDF

The microsurface *refraction* BSDF has the same structure as \\((3)\\) with two modifications:

* Uses fresnel to get how much energy was transmitted, not reflected: \\(1 - \bold F\\)
* The radiance is scaled by \\(\frac{\eta_o^2}{\eta_i^2}\\)

The latter describes the fact that a light beam is compressed when it enters into more dense medium, and it is expanded otherwise. This can derived by using a Snell's law and relating incoming and outgoing flux.

We get this general form of the ideal specular refraction BSDF:
\\[ \tag{4} f_s^m(\bold i, \bold o, \bold m) =
   (1 - F(\bold i, \bold m))
   \frac{\eta_o^2}{\eta_i^2}
   \frac{\delta_{\omega_m}(\bold m - \bold h(\bold i, \bold o))}{\lvert \bold i \cdot \bold m \rvert}
   \bigg| \frac{\partial \omega_h}{\partial \omega_i} \bigg|
\\]

Where:
* \\(\eta_o\\) is the IoR coefficient for the side of the medium the light is transmitted into (outgoing direction)
* \\(\eta_i\\) is the IoR for the incident side of the medium

What remains is to provide a definition of the half-angle direction for refraction. Then, compute how this half-angle direction reacts to changes in the indident direction:
\\[\bigg| \frac{\partial \omega_h}{\partial \omega_i} \bigg|\\].

### Big Jacobian press

The unnormalized half-angle direction for refraction:
\\[ \bold h_{unnorm} = -\eta_i \bold i - \eta_o \bold o \\]

This gives a vector that is perpendicular to the surface and points into the direction of the less dense medium (the reason why the negative signs are used). The incident \\(\bold i\\) and outgoing \\(\bold o\\) directions are tied by the Snell's law of refraction.

![microsurface](/microsurface-refraction/refraction-half-direction.png#center)

The corresponding normalized vector: \\(\bold h = normalize(\bold h_{unnorm})\\)

Let's find a differential solid angle \\(d\omega_h\\) centered around half-angle direction \\(\bold h\\) that corresponds to a differential solid angle \\(d\omega_i\\) centered around negated incident direction \\(\bold i\\).

![microsurface](/microsurface-refraction/refraction-jacobian.png#center)

From the definition of a solid angle and that the length of the \\(d\omega_i\\) cone is \\(\\eta_i\\):
\\[S = d\omega_i \eta_i^2\\]

The area of the \\(d\omega_h\\) cone cap:

\\[S_⟂ = S \lvert \bold h \cdot \bold i \rvert = d\omega_i \eta_i^2 \lvert \bold h \cdot \bold i \rvert\\]

The goal is to compute:
\\[d\omega_h = \frac{S_⟂}{\Vert\bold h_{unnorm}\Vert^2}\\]

Notice that \\( \Vert \bold h_{unnorm} \Vert^2 = (\bold h_{unnorm} \cdot \bold h)^2 = (\eta_i (\bold i \cdot \bold h) + \eta_o (\bold o \cdot \bold h))^2 \\)

The final expression for \\(d\omega_h\\) solid angle:
\\[d\omega_h = \frac
{d\omega_i \eta_i^2 \lvert \bold h \cdot \bold i \rvert}
{(\eta_i (\bold i \cdot \bold h) + \eta_o (\bold o \cdot \bold h))^2}
\\]

From here we get Jacobian coefficient:
\\[ \tag{5} \frac{d\omega_h}{d\omega_i} = \frac
{\eta_i^2 \lvert \bold h \cdot \bold i \rvert}
{(\eta_i (\bold i \cdot \bold h) + \eta_o (\bold o \cdot \bold h))^2}
\\]

Finally the microsurface BSDF \\((4)\\) can be written with specific Jacobian term for specular refraction (notice that \\(\eta_i^2\\) in the numerator and denominator cancels out):

\\[ \tag{6} f_s^m(\bold i, \bold o, \bold m) =
   (1 - F(\bold i, \bold m))
   \eta_o^2
   \frac{\delta_{\omega_m}(\bold m - \bold h(\bold i, \bold o))}{\lvert \bold i \cdot \bold m \rvert}
   \frac {\lvert \bold h \cdot \bold i \rvert} {(\eta_i (\bold i \cdot \bold h) + \eta_o (\bold o \cdot \bold h))^2}
\\]
