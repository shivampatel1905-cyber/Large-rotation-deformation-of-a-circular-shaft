# Large-rotation-deformation-of-a-circular-shaft
# Large Rotation Torsion of a Circular Shaft

A computational continuum mechanics project studying the torsion of a circular shaft undergoing **large rotation**. The Python implementation evaluates the deformation, strain, stress, and torque using both **small-strain linear elasticity** and a **finite-strain formulation**.

The project follows the ME 541 course project formulation for large-rotation torsion and is intended to be compared with finite element results and classical small-deformation torsion theory.

---

## 📌 Problem Description

Consider a straight circular shaft of radius \(R\) and length \(L\), subjected to torsion about its longitudinal \(Z\)-axis.

The deformation is defined in cylindrical coordinates as:

$$
r=R,\qquad
\theta=\Theta+\alpha Z,\qquad
z=Z
$$

where \(\alpha\) is the **twist per unit length**.

The corresponding Cartesian deformation map is:

$$
x=X\cos(\alpha Z)-Y\sin(\alpha Z)
$$

$$
y=X\sin(\alpha Z)+Y\cos(\alpha Z)
$$

$$
z=Z
$$

The implementation uses this deformation map to evaluate the kinematic and stress response of the shaft.

---

## 🎯 Objectives

The code is designed to:

* Implement the large-rotation deformation map numerically.
* Calculate the displacement field.
* Compute the deformation gradient \(F\).
* Evaluate \(J\), \(C\), and strain tensors.
* Calculate stresses using two material models.
* Extract torsional shear and axial normal stresses.
* Numerically integrate the cross-sectional stress distribution to obtain torque.
* Compare small-strain and finite-strain predictions.

These quantities correspond to the continuum-mechanics calculations specified in the project brief.

---

## 🧮 Kinematics

The deformation gradient used in the implementation is:

$$
F=
\begin{bmatrix}
\cos(\alpha Z) &-\sin(\alpha Z)&-\alpha y\\
\sin(\alpha Z)&\cos(\alpha Z)&\alpha x\\
0&0&1
\end{bmatrix}
$$

The code evaluates:

### Jacobian

$$
J=\det(F)
$$

### Right Cauchy-Green tensor

$$
C=F^TF
$$

### Green-Lagrange strain

$$
E=\frac{1}{2}(C-I)
$$

### Infinitesimal strain

$$
\epsilon=
\frac{1}{2}
\left(\nabla u+\nabla u^T\right)
$$

The project specification requires evaluation of both infinitesimal and Green-Lagrange strain measures for the large-rotation problem.

---

## 🔬 Material Models

Two constitutive models are implemented.

### Model 1 — Small-Strain Linear Elasticity

The infinitesimal strain tensor is used to calculate the Cauchy stress:

$$
\sigma^{(s)}
=
\lambda\,tr(\epsilon)I+2\mu\epsilon
$$

where

$$
\mu=\frac{E}{2(1+\nu)}
$$

and

$$
\lambda=
\frac{E\nu}
{(1+\nu)(1-2\nu)}
$$

This model represents the conventional small-strain linear elastic response.

---

### Model 2 — Finite-Strain Elasticity

The Green-Lagrange strain tensor is first used to calculate the second Piola-Kirchhoff stress:

$$
S=\lambda\,tr(E)I+2\mu E
$$

The Cauchy stress is then obtained by pushing the stress forward:

$$
\sigma^{(f)}
=
\frac{1}{J}FSF^T
$$

This formulation accounts for the finite deformation kinematics associated with the large rotation.

---

## 📐 Stress Extraction

The local circumferential direction is defined as:

$$
e_\theta=
[-\sin\theta,\cos\theta,0]
$$

and the axial direction as:

$$
e_z=[0,0,1]
$$

The torsional shear stress is extracted from:

$$
\sigma_{z\theta}
=
e_z^T\sigma e_\theta
$$

The implementation calculates this quantity for both constitutive models.

The axial normal stress is obtained from:

$$
\sigma_{zz}
$$

The project requires both torsional shear stress and axial normal stress to be evaluated.

---

## 🔩 Torque Calculation

The torque is obtained by numerical integration over the shaft cross-section:

$$
T=
\int_A r\sigma_{z\theta}\,dA
$$

The Python implementation evaluates this integral using **3-point Gauss quadrature** in both the radial and angular integration directions.

Two torque predictions are obtained:

* `T1` — Small-strain linear elasticity
* `T2` — Finite-strain elasticity

The numerical torque calculation follows the torque definition specified in the project.

---

## 💻 Python Implementation

### Requirements

* Python 3.x
* NumPy

Install NumPy using:

```bash
pip install numpy
```

### Running the Code

Run:

```bash
python shaft_torsion.py
```

The program will request:

```text
Radius
Length
Twist per unit length
```

It then asks for the coordinates of a material point:

```text
r
theta
z
```

followed by the material properties:

```text
Young's modulus
Poisson ratio
```

---

## 📥 Inputs

| Parameter | Description                | Unit  |
| --------- | -------------------------- | ----- |
| `R`       | Shaft radius               | m     |
| `L`       | Shaft length               | m     |
| `alpha`   | Twist per unit length      | rad/m |
| `r`       | Radial coordinate of point | m     |
| `theta`   | Angular coordinate         | rad   |
| `z`       | Axial coordinate           | m     |
| `E_mod`   | Young's modulus            | Pa    |
| `nu`      | Poisson's ratio            | –     |

---

## 📤 Outputs

The program calculates and displays:

### 1. Original Position

$$
(X,Y,Z)
$$

### 2. Deformed Position

$$
(x,y,z)
$$

### 3. Displacement

$$
u=(u_x,u_y,u_z)
$$

### 4. Deformation Gradient

$$
F
$$

### 5. Jacobian

$$
J=\det(F)
$$

### 6. Stress Tensors

* Small-strain Cauchy stress
* Finite-strain Cauchy stress

### 7. Shear Stress

$$
\sigma_{z\theta}
$$

### 8. Axial Stress

$$
\sigma_{zz}
$$

### 9. Torque

Torque predictions from both material models are reported.

---

## 📊 Classical Torsion Comparison

The computational results can be compared with the classical small-deformation torsion solution:

$$
\tau(r)=G\alpha r
$$

and

$$
T=GJ_p\alpha
$$

where the polar moment of area for a circular shaft is:

$$
J_p=\frac{\pi R^4}{2}
$$

These equations provide a baseline for evaluating how the classical solution changes in the large-rotation regime.

---

## 🔄 Project Validation

The complete project is intended to compare three approaches:

1. **Continuum mechanics Python implementation**
2. **Finite element simulation**
3. **Classical small-deformation torsion theory**

The primary comparison quantities are:

* Torsional shear stress vs. radius
* Axial normal stress vs. radius
* Torque vs. twist
* Effect of increasing twist
* FEM mesh refinement

A quantitative error measure such as relative torque error can also be used:

$$
\text{Relative Error}
=
\frac{|T_{FEM}-T_{code}|}
{|T_{code}|}
$$

These comparisons are part of the required project validation.

---

## 📁 Repository Structure

```text
Large-Rotation-Torsion/
│
├── shaft_torsion.py
├── README.md
│
├── FEM/
│   └── FEM model files
│
├── results/
│   ├── stress_plots/
│   ├── torque_comparison/
│   └── mesh_convergence/
│
└── report/
    └── ME541_project.pdf
```

---

## 🛠️ Tools & Technologies

* **Python**
* **NumPy**
* Continuum Mechanics
* Finite Deformation Kinematics
* Linear Elasticity
* Numerical Integration
* Finite Element Analysis

---

## 🚧 Scope & Limitations

The current Python implementation focuses on the **continuum mechanics portion** of the project.

The complete project framework additionally requires FEM simulation, mesh refinement, and comparison against classical torsion theory.

The current code evaluates the response at a specified material point and uses numerical integration over the cross-section for torque calculation.

---

## 📚 Reference

**ME 541 Course Project — Large Rotation Torsion of a Circular Shaft**

The project requires derivation and implementation of the large-rotation kinematics, stress calculations using small- and finite-strain models, numerical torque integration, FEM simulation, and comparison with classical torsion theory.

