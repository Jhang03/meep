---
# 2d Cell with Out-of-Plane Wavevector
---

A 2d cell with a `k_point` that has a *non-zero* $z$ component $\beta$ (e.g., planewaves incident on a 2d structure from an out-of-plane direction, propagating modes of fiber waveguides with 2d cladding cross section, etc.) could be accomplished by a "1-pixel-thick" 3d simulation with complex fields (this is done by the `kz_2d="3d"` option). However, Meep will by default model the $e^{i \beta z}$ dependence using a modified 2d simulation with complex fields (this is done by the `kz_2d="complex"` option), which improves performance with no loss in accuracy.   As a further optimization, Meep can also model this problem with with "real" fields via `kz_2d="real/imag"`, but the fields must be interpreted in a special way. 

Mathematically, an $e^{i \beta z}$ dependence ($k_z = \beta$) of the fields can be treated by including an $i\beta\hat{z} \times {}$ cross-product to the curls of Maxwell's equations, which couples the $E_z$ and $H_z$ polarizations.   Since this term is complex ($\sim i \beta$), the electromagnetic fields are complex.

However, an additional trick is possible.  Since the $i\beta\hat{z} \times {}$ complex term only couples $E_z$ and $H_z$ polarizations to one another, then we can choose the $H_z$ polarization ($E_x, E_y, H_z$ fields) to be purely real while the $E_z$ polarization ($H_x, H_y, E_z$ fields) is purely *imaginary*.   The `kz_2d="real/imag"` option does precisely this, but stores the purely imaginary $E_z$ polarization as the "real" parts of the fields, both internally and in the output.  So, if you use the `kz_2d="real/imag"` and you output both $E_x$ and $E_z$, the output will be real numbers, but you should multiply $E_z$ by $i$ to get the actual fields.    This requires some care, which is why this option is not the default.  The good news is that calculations of flux, energy, forces and similar real quantities are insensitive to this implicit $i$ factor (the $i$ cancels), so the built-in calculations do the right thing with no modificatio