# Notes

Note-taking for things I want to remember.

# Precomputen't atmosphere

Idea here is to use a precomputed atmosphere, but replace the precomputed textures with approximations

so far [this shader by Jodie](https://www.shadertoy.com/view/ttSGzh) looks quite promising, 
as it also has an optical depth approximation for looking up

This approximation goes as follows:

`d = b_scaled + sqrt(r_scale*r_scale + b_scaled*b_scaled - r_planet*r_planet)`
where
 - `b` is the dot product with the down vector,
 - `b_scaled` is `b * r_planet`,
 - `r_scale` is `s_scale + r_planet`,
 - `r_planet` is the planet radius,  
 - and `s_scale` is the atmosphere scale height

when looking up, this is rougly equivalent of the integral of 
`sqrt((r_planet + t * cos(theta))^2 + (t * sin(theta))^2)`
where `theta` is the angle between the view ray and up

Further investigation tells me this is probably similar to `-1 / (x - 2)`, 
with the points at x = -1 and x = 1 set to the optical depth when looking up and down respectively
NOTE: this is not steep enough near 0, so might need some extras. Mayab try pade approximation?

I'm not entirely sure how to properly handle the proper optical depth via light yet, 
and Jodie does this with the integral of `exp(-a * x - b * (1 - x))` with x from 0 to 1,
and a being the scattering over the view ray, and b being the scattering over the sun ray.

Jodie turns this around when dividing however, not sure why.
aka it becomes `(exp(-a) - exp(-b)) / (b - a)` instead of `(exp(-a) - exp(-b)) / (a - b)`

As for multiple scattering, I'm not sure how to handle that. The tables generated for it
are 2d, and only depend on the height and sun angle, so I think for that table an `exp(-x) + extras` might be able to do it.
Still unsure how to intergrate that into the full system tho, probably just added to the scattering for the view and light rays.
