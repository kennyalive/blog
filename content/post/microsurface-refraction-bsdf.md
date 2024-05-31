---
title: "Microsurface Refraction BSDF Derivation"
date: 2024-05-21T23:13:12+02:00
enable_math: true
draft: true
---

\\[ \tag{1} f_s(\bold i, \bold o, \bold n) = 
   \underset{H^2(\bold n)}{\int} \bigg| \frac{\bold i \cdot \bold m }{\bold i \cdot \bold n} \bigg|
   f_s^m(\bold i, \bold o, \bold m)
   \bigg| \frac{\bold o \cdot \bold m }{\bold o \cdot \bold n} \bigg|
   G(\bold i, \bold o, \bold m) D(\bold m) d\omega_m
\\]

This article is about getting expression for the microsurface BSDF \\(f_s^m(\bold i, \bold o, \bold m)\\) in the case of ideal specular refraction.

### Warmup

At first, here is an expression for the case of ideal specular *reflection*. Just to have it here. It's not needed further:


\\[ \tag{2} f_s^m(\bold i, \bold o, \bold m) =
   F(\bold i, \bold m) \frac{\delta_{\omega_i}(\bold i - reflect(\bold o, \bold m))}{\lvert \bold i \cdot \bold m \rvert}
\\]

The dirac delta function is to reflect the light only in a single direction. The fresnel term is to reflect only a portion of the light as defined by the fresnel physics. The \\(\lvert \bold i \cdot \bold m \rvert \\) term is put in the denominator, so it cancels the same term when we integrate over all incident directions. Written in this form, the microsurface BSDF allows the light to be scattered only in a single direction and the reflected radiance is scaled by the fresnel factor.

Because \\((1)\\) integrates over all microsurface normals but the delta function \\(\delta_{\omega_i}\\) uses the measure related to incident direction, we need to rewrite \\((2)\\), so it is defined through a delta function with a microsurface normal solid angle measure - \\(\delta_{\omega_m}\\). In this form it can be injected in the integral \\((1)\\).

Updating measure introduces an additional Jacobian term \\(\frac{\partial \omega_h}{\partial \omega_i}\\). The microsurface specular *reflection* BSDF expression can be written like this:

\\[ \tag{3} f_s^m(\bold i, \bold o, \bold m) =
   F(\bold i, \bold m) \frac{\delta_{\omega_m}(\bold m - \bold h(\bold i, \bold o))}{\lvert \bold i \cdot \bold m \rvert}
   \bigg| \frac{\partial \omega_h}{\partial \omega_i} \bigg|
\\]

### Microsurface refraction BSDF

The microsurface *refraction* BSDF has the same structure as \\((3)\\) with two modifications:

* Uses fresnel to get how much energy was transmitted, not reflected: \\(1 - \bold F\\)
* The radiance is scaled by \\(\frac{\eta_o^2}{\eta_i^2}\\)

The latter describes the fact that a light beam is compressed when it enters into more dense medium, and it is expanded otherwise. This can derived by using a Snell's law and relating incoming and outgoing flux. PBRT book and Veach thesis have more details.

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

What is left is to provide a definition of the half-angle direction for the refraction and to compute how this half-angle direction reacts to the changes in the indident direction:
\\[\bigg| \frac{\partial \omega_h}{\partial \omega_i} \bigg|\\].

### Big Jacobian press

The definition of the half-angle direction for refraction:
\\[ \bold h = -\eta_i \bold i - \eta_o \bold o \\]

This gives a vector that is perpendicular to the surface and points into the direction of the less dense medium (the reason why the negative signs are used). The incident \\(\bold i\\) and outgoing \\(\bold o\\) directions are tied by the Snell's law of refraction.

![microsurface](/microsurface-refraction/refraction-half-direction.png#center)

