# Pinhole Camera Model Verification in Blender

This note focuses on verifying the pinhole camera model in Blender and
retrieving geometric quantities via the Blender Python API.


![alt text](docs/resource/pinhole_homogeneous_transformation.png)
**Figure 1.** Reference coordinate frames and homogeneous transformation
defined by the pinhole camera model in OpenCV.

![alt text](docs/resource/pinhole_blender.PNG)
**Figure 2.** Equivalent camera and coordinate frame setup implemented
in Blender to reproduce the OpenCV pinhole camera model.

`>>>`   


The rigid transformation from object frame to camera frame is defined as:

- $\mathcal{F}_c$: OpenCV camera coordinate frame
- $\mathcal{F}_{c-{blender}}$: Blender camera coordinate frame
- $\mathcal{F}_o$: object coordinate frame
- $(X_c, Y_c, Z_c)$: axes of the camera frame
- $(X_o, Y_o, Z_o)$: axes of the object frame
- ${}^{c}\mathbf{T}_{o}$: rigid transformation from object frame to camera frame
- $\mathbf{X}_i^{3D} \in \mathbb{R}^3$: 3D object point expressed in $\mathcal{F}_o$
- $\mathbf{x}_i^{2D} \in \mathbb{R}^2$: projected 2D image point
- $\mathbf{K}$: camera intrinsic matrix
- $(u, v)$: image plane coordinates

$$
{}\mathcal{F}_{c-{blender}}
=  
\begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & -1 & 0 & 0 \\
0 & 0 & -1 & 0 \\
0 & 0 & 0 & 1 \\
\end{bmatrix} \ \mathcal{F}_{c}
$$


$$
{}^{c}\mathbf{T}_{o}
=
\begin{bmatrix}
^{c}\mathbf{R}_{o} & ^{c}\mathbf{t}_{o} \\
\mathbf{0}^\top & 1
\end{bmatrix}
$$