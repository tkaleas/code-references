  
  
# Real Time Rendering Notes
  
  
Notes while reading [Real Time Rendering](http://www.realtimerendering.com/ )
  
## Chapter 2: Graphics Pipeline
  
  
- Pipeline: executes in parallel with each stage dependant on the previous stage.
- Bottleneck : speed determined speed of the entire pipeline, starved in stage waits for time for this stage to be done. 
- Rendering Speed: FPS or Hertz
  
### Stages
  
  
- Application Stage: Collision Detection, animation, physics, etc.
- Geometry Processing: transforms, projections, geometry handling.
-  Rasterization Stage: takes in a triangle, exports pixels related to this triangle.
- Pixel Processing: per pixel operations 
  
#### Application Stage
  
  
-  Full User Control in Code
- Computer Shader: use gpu to do parallel work not for graphics
- Rendering Primitives: 
- Collision Detection, culling acceleration
  
#### Geometry Processing
  
  
- Vertex Shading -> Projection -> Clipping -> Screen Mapping
  
#### Vertex Shading
  
  
- Transforms to correct Space or Coordinate System
- Coordinates of Object: model coordinates
- World Space: World Coordinates, absolute coordinates in 3d space of object
- View Transform: place camera at the origin and aim it
	- Look in direction of negative Z axis
	- Y axis pointing up, Z axis pointing to the right.
	- Some look down +Z axis, difference is semantic ()right hand versus left hand coordinates)
- Camera Space = View Space
- some Shading operations can occur during this phase.
- Projection
	- Orthographic or Perspectice
- Clipping
	- Transforms view volume into unit cube with extreme points at (1,1,1) (-1,-1,-1)
- Orthographic view volume is just a box
- Perspectice view volume is a frustum.
- 4x4 matrices to both
  
#### Optional Vertex Processing
  
  
- Tesselation: series of stages to convert vertices into larger sets of vertices with new triangles based on view distances.
- Geometry Shader: many uses, such as particle generation.
	-Particle represented by single vertex, converted into square that faces viewer.
- Stream Output: GPU as a geometry engine, output these back to memory for use by CPU or GPU.
  
#### Clipping
  
  
- moving vertices to be inside the view volume or making new verts to fit this.
  
#### Screen Mapping
  
- moving from clip coordinates to coordinates of the actual screen.
  
#### Pixel Shading
  
  
- texturing occurs at this stage
  
#### Merging
  
  
- z buffer culling, alpha channels to blend
- stencil buffer used or can be used here
- post process shaders
  
#### Resources
  
  
- Blinn' *A Trip Down the Graphics Pipeline*
  
### Transforms Chapter 4
  
  
- Linear Transform: perserves vector addition, scalar multiplications, parallel lines
- Affine Transform: linear transform and translation, 4x4 matrix
	-use homogenous coordinates for positions or directions (4 sized vectors)
  
#### Basic Transforms
  
  
- Translation
- Rotation
- Scaling
- Shear
- Euler
- Orthographic Projection
- Perspectice Projection
- Slerp Transform
  
#### Translation
  
  
```
1 0 0 Tx
0 1 0 Ty
0 0 1 Tz
0 0 0 1
```
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;begin{bmatrix}%201%20&amp;%200%20&amp;%200%20&amp;%20d&#x5C;&#x5C;0%20&amp;%201%20&amp;%200%20&amp;%20d&#x5C;&#x5C;0%20&amp;%200%20&amp;%201%20&amp;%20d&#x5C;&#x5C;0%20&amp;%200%20&amp;%20c%20&amp;%20d&#x5C;&#x5C;%20%20&#x5C;end{bmatrix}"/></p>  
  
  
#### Rotation
  
  
Rx(phi)
```
1 0 			0 			   	0
0 cos(phi) -sin(phi) 	0
0 sin(phi) cos(phi) 	0
0 0 			0 				1
```-
Ry(phi)
```
cos(phi) 	0 		sin(phi) 	0
0 				1 		-sin(phi) 	0
-sin(phi) 	0 		cos(phi) 	0
0 				0 		0 				1
```
Rz(phi)
```
cos(phi) 	-sin(phi) 	0 			0
sin(phi) 	cos(phi) 	0 			0
0 				0 				1 			0
0 				0 				0 			1
```
  
#### Scaling
  
```
Sx 0 0 0
0 Sy 0 0
0 0 Sz 0
0 0 0   1
```
#### Concatenation
  
- multiply matrices together to concatenate transforms
- Order is TRSp = T(R(Sp)) where T R S are matrices, and p is a point
- Called rigid body transforms when they are only translations and rotations
  
  
#### Projections
  
- perspective affects w coordinate, ortho does not
##### Orthographic
  
- paralel lines remain parallel after projections
- axis aligned bound box is a common matrix
- concatenation of a scaling and translation matrix
- essential scalling for Field of View, translation to move camera to the origin.
  
  
## Chapter 5: Shading
  
- gooch shading model: 
```
c_cool = (0 0 0.55)+0.25*c_surface
c_warm = (0.3 0.3 0 )+0.25*c_surface
c_highlit = (1 1 1)
t = n dot l + 1)/ 2
r = 2(n dot l )n-l
s = clamp01(100(r dot v)-97)
c_shaded = s*c_highlight+(1-s)(t*c_warm+(1-t)*c_cool)
```
### Light Sources
  
- Directional Lights: no location, light direction remains constant
- Punctual Lights: location, emanates out radially.
	- inverse square light attenuation:
	```
	r_0= fixed reference distance
	r = distance between light and shaded point
	c_light(r)=c_light_0(r_0/r)^2
	```
	-spotlight: point light with directional falloff
		-umbra angle, penumbra angle theta_u and theta_p
		-spotlight directional vector s, also expressed theta_s between s and reversed light vector
```
c_light = c_light_0*f_dist(r)*f(dir(l)
t = clamp01((cos(theta_s)-cos(theta_u)/(cos(theta_p)-cos(theta_u)))
f_dist_f(l)=t^2
f_dir_t(l)=smoothstep(t)
```
  
### Implementions
  
- most shading performed per pixel, not per vertex unless perfromance needed
- interpolation errors across verts
- normalizing vectors: normalizing vectors BEFORE interpolation can lead to incorrect results (interpolate first then normalize)
### Material Systems
  
- artist facing abstraction of the visual appearance of a surface
- material is not a surface
- material system should divide shader functions into separate elements and control how they are combined
	- geometric processing like morphing, tesselation, instancing, clipping
	- compositing operations, pixel discard and blending
	- computation of which shading model to use itself
	- individually selecting material features
	- computation of shading model and light source evaluation parameters (deffered rendering)
- dynamic branching  or during compile time via conditional preprocessing for feature enabling/disabling
- Strategies for Material System Feature Branching
	- Code Reuse : shared filed with #includes
	- Subtractive : uber/supershader
	- Additive: text or visual graph editor software
	- Templated Based: interface for general shader type (such as UE4 material domain: surface/light/post-processing
- WebGL Insights [301]
	- desgrined shader pipeline implementations
- shaders often written in custom language dialect: Destiny, UE4, Unity
  
### Aliasing and Antialiasing
  
- rendering is a sampling task
- goal of sampling to represent a continuous signal digitally at discrete intervals
- sampled signal needs to be filtered to reconstruct accurate picture of original signal
- sampling frequency has to be more than twice the maximum frequency of the signal to be sampled
- **Nyquist rate** or **Nyquist limit** is the frequency, and its called *sampling theorem*
- Box Filter: Nearest Neighbor
  
  
  