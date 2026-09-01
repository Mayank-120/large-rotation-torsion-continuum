# large-rotation-torsion-continuum
Continuum mechanics study of a circular shaft under large-rotation torsion. MATLAB implementation computing the deformation gradient, kinematic tensors, and Cauchy stress under small-strain and finite-strain models, with torque by Gauss quadrature. Validated against Saint-Venant theory and 3D FEM; captures the Poynting effect.

large-rotation-torsion-continuum
Continuum mechanics study of a circular shaft under large-rotation torsion, comparing a small-strain model, a finite-strain model, and classical Saint-Venant theory, with results validated against a 3D finite element simulation.
Objective
Study torsion of a circular shaft undergoing large rotation by combining a continuum mechanics implementation in MATLAB with a finite element simulation, and compare both against classical small-deformation torsion theory to establish where the classical solution breaks down.
Problem definition
A straight circular shaft of radius a and length L is twisted about its axis such that each cross-section rotates through an angle proportional to its axial coordinate. In cylindrical coordinates the deformation is a pure twist of magnitude α per unit length.
Parameter
Symbol
Value
Shaft radius
a
0.05 m
Shaft length
L
1 m
Twist per unit length
α
0.1 rad/m
Young's modulus
E
200 GPa
Poisson's ratio
ν
0.3
Continuum formulation
Deformation map. The reference position (X, Y, Z) maps to the current configuration through a rotation of each cross-section by αZ about the axis.
Deformation gradient. F = ∂x/∂X is computed two ways — analytically, and numerically by finite differences with step h = 1e-6 — and the two are compared using the Frobenius norm ‖F_num − F_analytical‖ as a correctness check.
Kinematic quantities. From F the code evaluates:
Jacobian J = det(F), which should equal 1 for isochoric deformation
Right Cauchy-Green tensor C = FᵀF
Left Cauchy-Green tensor B = FFᵀ
Green-Lagrange strain E = ½(C − I)
Infinitesimal strain ε = ½(∇u + ∇uᵀ)
Material models
Model 1 — Small-strain linear elasticity. Cauchy stress computed directly from the infinitesimal strain tensor ε via the isotropic linear elastic law.
Model 2 — Finite-strain elasticity. The second Piola-Kirchhoff stress S is computed from the Green-Lagrange strain E, then pushed forward to the Cauchy stress. This retains the geometric nonlinearity that Model 1 discards.
Torque is obtained in both cases by integrating σ_zθ · r over the cross-section using 3×3 Gauss quadrature.
Finite element simulation
Geometry: cylinder matching the continuum model dimensions
Material: isotropic linear elastic
Fixed end (Z = 0): all degrees of freedom constrained
Loaded end (Z = L): prescribed rotation φ = αL about the axis
Static structural analysis
Mesh convergence
Mesh
Element size (m)
Elements
T_FEM (N·m)
Coarse
0.02
1,734
75,540
Medium
0.01
22,440
75,551
Fine
0.005
167,232
75,551
Torque changes by 0.015% between coarse and medium and is unchanged between medium and fine, so the medium mesh is converged.
Results
Deformation gradient verification
α (rad/m)
‖F_num − F_analytical‖
0.05
1.05e-10
0.1
2.55e-10
0.3
2.25e-09
0.5
6.25e-09
Errors of order 1e-10 confirm the finite-difference implementation is correct.
Stresses and torque at α = 0.1 rad/m
Model
σ_zθ at r = a (Pa)
σ_zz at r = a (Pa)
Torque (N·m)
Model 1 (small strain)
3.826939e+08
−1.152885e+09
7.514177e+04
Model 2 (finite strain)
3.827107e+08
3.365385e+06
7.514397e+04
Classical / FEM
3.846154e+08
0 (assumed)
7.551905e+04
Torque versus twist
α (rad/m)
Model 1 (N·m)
Model 2 (N·m)
Classical (N·m)
Δ Model 1
Δ Model 2
0.05
3.771234e+04
3.771261e+04
3.775953e+04
0.12%
0.12%
0.1
7.514177e+04
7.514397e+04
7.551905e+04
0.50%
0.50%
0.3
2.164383e+05
2.164951e+05
2.265572e+05
4.47%
4.44%
0.5
3.313710e+05
3.316126e+05
3.775953e+05
12.24%
12.18%
The classical solution scales linearly with α; both continuum models fall increasingly below it as the twist grows, reaching a 12% discrepancy at α = 0.5 rad/m.
Key observations
J = det(F) = 1 for every value of α, confirming the deformation is isochoric, as expected for pure torsion.
Classical torsion theory is accurate only for small twist. Below α ≈ 0.05 rad/m all three approaches agree to within 0.12%; by α = 0.5 rad/m the classical result overestimates torque by over 12%.
The finite-strain model predicts a non-zero axial normal stress σ_zz, absent from classical theory. This is the Poynting effect — a twisted bar tends to elongate — and it is a hallmark of finite-strain torsion that no small-strain formulation can reproduce.
FEM results agree with the continuum implementation to within 0.5%, cross-validating both.
Repository contents
Code
Usage
Set the shaft geometry, material properties, twist α, and the material point of interest in the input block at the top of the main script, then run. The script prints the kinematic tensors and stresses at that point and generates the radial stress profiles and torque-versus-twist curves.
Limitations
Material response is isotropic linear elastic in both models; no plasticity or hyperelastic strain-energy function is used.
The prescribed deformation map assumes cross-sections remain plane and circular, which holds exactly for a circular shaft but not for other sections.
Only static loading is considered.