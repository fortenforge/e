---
layout: post
title:  "Eclipse-posting Day 1 - The Eddington Expedition"
date:   2024-03-30 3:12:44 -0500
categories: eclipse
use_math: true
tags: eclipse math
---

In 1919, English astronomer Sir Arthur Eddington embarked on a journey to the island Príncipe just off the west coast of Africa in order to witness a total solar eclipse. His goal? To test a novel theory put forth by a certain German physicist a few years earlier: general relativity.


<!--more-->

General relativity predicts that light passing a large mass is deflected due to the way the mass curves spacetime. If you were able to measure the relative positions of stars at night versus the day, you would expect to see differences due to the lensing of the stars’ light as it passes by the sun.

The problem is that you can’t see any stars during the day—the sun’s light drowns everything else out. Except of course, during a total solar eclipse, when the moon blocks all light from the sun beyond its faint corona, plunging the part of the Earth in the moon’s shadow into darkness.

The solar eclipse taking place on May 29th, 1919 was a particularly long one, lasting almost 7 minutes at the point of maximum totality; it was visible along a narrow band passing through most of Africa and South America. Furthermore, during totality the sun would pass in front of a particularly bright star cluster—the [Hyades](https://en.wikipedia.org/wiki/Hyades_(star_cluster))—which would allow the expedition to measure the deflection of multiple points.

Eddington organized two expeditions, one to Príncipe and one to the town of [Sobral](https://en.wikipedia.org/wiki/Sobral,_Cear%C3%A1) in Brazil to maximize the chance of getting useful data from the event. The photographic plates obtained by the back-up telescope proved the most clear, displaying a deflection corresponding to Einstein’s predictions.

![plate](/e/assets/eddington/plate.jpeg){: .center-image }

The results of the experiment were published at a joint session of the Royal Society and Royal Astronomical Society on November 6th, 1919. There was intense press fanfare surrounding the report, and this experiment is generally credited with launching Einstein—who was well-known only within the physics community at the time—into superstardom.

## Newtonian Deflection

When I first learned about the Eddington expedition I was puzzled by the mention that general relativity predicts an angular deflection of twice the magnitude of the deflection under classical physics. Why would light be deflected at all under Newtonian mechanics and how could you calculate the deflection?

I'll explain the derivation in this section—you'll need only knowledge of classical mechanics and a little calculus to follow.

Imagine a particle of light with mass \\(m\\) that is traveling past a much more massive body (the sun) with mass \\(M\\). The force that the particle experiences is:

\\[\vec{F} = \frac{GMm}{r^2} \hat{r} = m\vec{a}\\]

Suppose that the perpendicular component of the distance from the particle to the sun is \\(b\\) and the parallel distance is \\(s\\). We’ll assume that \\(b\\) stays approximately the same throughout the path of the particle, but that \\(s\\) varies.

![diagram](/e/assets/eddington/diagram.png){: .center-image }

By the Pythagorean theorem \\(r^2 = s^2 + b^2\\). We’ll further assume that \\(ds = cdt\\) where \\(c\\) is the speed of light—in other words the particle moves at an unchanging speed \\(c\\) along the vector parallel to the sun. The change in speed of the particle along the perpendicular component is:

\\[\Delta v_{\perp} = \int_{-\infty}^{\infty} \frac{GM}{b^2 + s^2}dt = \int_{-\infty}^{\infty} \frac{GM}{c(b^2 + s^2)} ds\\]

Simplifying further:

\\[\frac{GM}{b^2 c} \int_{-\infty}^{\infty} \frac{ds}{1 + (s/b)^2} = \frac{GM}{b^2 c} \int_{-\infty}^{\infty} \frac{bdu}{1 + u^2}\\]

(note the \\(u\\)-substitution in the final step above). Now, recall that:

\\[\int \frac{1}{1+x^2} = \tan^{-1} x\\]

So:

\\[\int_{-\infty}^{\infty} \frac{dx}{1 + x^2} = \tan^{-1}(x) \Bigg\|_{-\infty}^{\infty} = 2\\]

Thus:

\\[\Delta v_{\perp} =  \frac{2GM}{bc}\\]

So the velocity vector goes from:

\\[\langle 0,  c \rangle \rightarrow  \left \langle \frac{2GM}{bc}, c \right \rangle\\]

The deflection angle relative to the starting trajectory is the arc-tangent of the ratio of the final velocity components:

\\[\Delta \theta = \tan^{-1} \frac{2GM}{bc^2} \approx \frac{2GM}{bc^2} \\]

## General Relativistic Deflection

Under general relativity, light travels along *geodesics*—essentially “straight lines” in spacetime. Of course, spacetime itself can be curved by massive objects. The curvature is precisely defined by [Einstein’s field equations](https://en.wikipedia.org/wiki/Einstein_field_equations), but exact solutions are difficult to obtain outside of simplifying assumptions.

One such simplification is to consider only the influence one large mass \\(M\\). Photons in this scenario follow the [Schwarzschild geodesics](https://en.wikipedia.org/wiki/Schwarzschild_geodesics).

If you use the Schwarzschild solution to Einstein’s field equations to calculate the deflection (see [this video](https://www.youtube.com/watch?v=MxWATpSGEgY) for the calculation) you produce:

\\[\Delta \theta = \frac{4 GM}{bc^2}\\]

In other words the deflection under general relativity is exactly twice as under classical mechanics.

If you [carry out the calculations](https://www.wolframalpha.com/input?i=%28%284+*+G+*+%28mass+of+sun%29%29+%2F+%28%28speed+of+light%29%5E2+*+%28radius+of+sun%29%29%29+radians+to+arcseconds) for a star positioned right on the edge of the sun (in other words, setting \\(b\\) to the sun’s radius) you’ll get a deflection of 0.876 arc-seconds under Newtonian physics and a deflection of 1.75 arc-seconds under general relativity. This is a difference of about 2.5 ten thousandths of a degree—it’s still incredible to me that Eddington’s team was able to measure such a small arc, but measure it they did.

The measurements at Sobral resulted in a calculated deflection of 1.94 ± 0.12 arc-seconds—close enough to 1.75 to validate general relativity.

## Further Reading

* Eddington’s [original report](https://zenodo.org/records/1432106) to the Royal Society. It’s fascinating, and surprisingly easy to understand!
* This [Vox video](https://www.youtube.com/watch?v=HLxvq_M4218), from which I first learned about the experiment.
* This [Guardian article](https://www.theguardian.com/science/2019/may/12/100-years-on-eclipse-1919-picture-that-changed-universe-arthur-eddington-einstein-theory-gravity), which includes more details about the expedition itself.
