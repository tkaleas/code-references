---
markdown:
  image_dir: /assets
  path: rt_rendering_output.md
  ignore_from_front_matter: true
  absolute_image_path: false

export_on_save:
  markdown: true
---

# Real Time Rendering Notes

Notes while reading [Real Time Rendering](http://www.realtimerendering.com/)

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

$$
T=
\begin{bmatrix} 
1 & 0 & 0 & Tx\\
0 & 1 & 0 & Ty\\
0 & 0 & 1 & Yz\\
0 & 0 & 0 & 1\\  
\end{bmatrix}
$$

#### Rotation

$$
R_x(\phi)=
\begin{bmatrix} 
1 & 0 & 0 & 0\\
0 & cos(\phi) & -sin(\phi) & 0\\
0 & sin(\phi) & cos(\phi)& 0\\
0 & 0 & 0 & 1\\  
\end{bmatrix}
$$

$$
R_y(\phi)=
\begin{bmatrix} 
cos(\phi) & 0 & sin(\phi) & 0\\
0 & 1 & 0 & 0\\
-sin(\phi) & 0 & cos(\phi)& 0\\
0 & 0 & 0 & 1\\  
\end{bmatrix}
$$

$$
R_z(\phi)=
\begin{bmatrix} 
cos(\phi) & sin(\phi) & 0 & 0\\
sin(\phi) & cos(\phi) & 0 & 0\\
0 & 0 & 0& 0\\
0 & 0 & 0 & 1\\  
\end{bmatrix}
$$

#### Scaling

$$
S=
\begin{bmatrix} 
S_x & 0 & 0 & 0\\
0 & S_y & 0 & 0\\
0 & 0 & S_z& 0 \\
0 & 0 & 0 & 1  \\  
\end{bmatrix}
$$

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
	-box filter placed over each sample point, then scaled so that it coincides with the point.
- Tent Filter: interpolation between samples, linear
- Low Pass Filter: $sin(2\pi f)$ where $f$ is frequency of the component
  - Removes all frequences higher than a certain frequency of the filter, blurs
  $$sinc(x)=\frac{sin(\pi x)}{\pi x}$$
  - this gets us continuous signal back but we cannot use
  - 
#### Resampling

- upsampling vs. downsampling
- resample using the new function at continuous intervals
- downsampling: trickier since resampling can lead to aliasing
	- increase filter width to reduce higher frequency content
    - akin to blurring then resampling at lower rez
    - 
#### Screen Based Antialiasing 
 - general strategy of screen based anti aliasing is to use sampling pattern for pixel then weight and sum samples to produce pixel color.
  $$ p(x,y)=\sum_{i=1}^{n}{w_i\bm{c}(i,x,y)}$$
- n is the number of samples for the pixel
- Super Sampling/Full Scene Antialiasing: render scene at higher resolution and filter neighbors to create an image.
- MultiSampling Anti Aliasing (MSAA): compute shade once per pixel, then share result among the samples
- TAA: temporal antialiasing, a class of tequniques using results from previous frames to improve image
  - no additional samples needed
  - reprojection: uses a velocity buffer to better correlate frames
  - good use with deferred shading which is not compatible with MSAA
- MSAA sampling patterns are hard-coded 

### Transparency Alpha and Compositing
- Screen Door Transparency: checkboard pattern on object (every other pixel is rendered)
  - usually every other pixel is close enough that checker pattern not visible
- Stochastic Transparncy: screen door masks with stochastic sampling
- Alpha Blending: alpha is opacity, or coverage of fragment for sampling techniques
#### Blending Order
- Over Operator: $ \bm{c}_o=\alpha_s\bm{c}_s+1(1-\alpha_s)\bm{c}_d$
- Additive Blending: $\bm{c}_o=\alpha_s\bm{c}_s+\bm{c}_d$
#### Premultiplied Alphas
- Premultiplied Alphas makes the compositing equation much more efficient
  - over and additive blending easier, as all you need to do is add source color during blending.
- Non-Premultiplied Alphas: separate storage of alpha value, must be careful with linear interpolation as black frindes can result.


### Display Encoding
- Gamma Correction: raise to power 1/2.2
- based on CRT monitor display functions, we want to cancel out the effect of this so apply inverse function to bring back to a normal range.


## Chapter 6: Texturing
- texels: to differentiate from pixels on the screen
- projector function: changes from (x,y,z) to (u,v) coordinates
	- cylindrical, planar, unwrapped UV per vertex (mesh parameterization)
	- cube map another form of directional coordinate space

## Chapter 19: Acceleration Algorithms

### Level of Detail
- simpler versions of objects based on criteria to improve performance/rendering
- reudction in vertex processing (and pixel shading costs)
- Generation, Selection, Switching
#### LOD Switching
- discrete: pop LOD's off and on
- blend LODS: fade in LOD2 then fade out LOD1, render from transparent to opaque.
  - short transition intervals
- alpha LODS: very continuous, avoids popping
  - gradually bring in LODs 0-1 alpha, if invisible do not render
#### LOD Selection
- benefit function: pick appropriate LOD based function
  - distance: range
  - screen space coverage
  - estimation of radius of projected sphere on screen
    - object: (circle center point c, radius r)
    - viewer(position v, direction d)
  $$p=\frac{nr}{d\cdot{(v-c)}}$

#### Terrain Rendering
- **Clipmap**: courser levels of geometry like mipmapping.
  - smoothly interpolate at seams
  - [1078],[82],[555]
- Tiles: breakup heightfield into tiles at these multiple levels
- **Chunked LOD**
  - represent terrain using n levels of detail
  - structure encoded in quad-tree and traversed at root for rendering
  - render ONLY if screen space error is below threshold, otherwise visit children.
    - results in better resolution near viewer
    - w is width of screen, d is distance to camera from terrain tile, theta is horizontal field of view (radians), epsilon is geometric error
  - $s=\frac{\epsilon w}{2d\tan{\frac{\theta}{2}}}$
  - Hausdorff Distance used for geometric error [906,1605]
  - morphing using higher LOD at boundaries between LODs
  - crack repair:
    - bridge gap between 2 tiles [324,670]
    - vertex shader solution [1720]
    - [299,244]

##### Terrain References
- [82] Terrain Rendering Using GPU-Based Geometry, *Gpu Gems 2*
- [555] Landscape Creation and Rendering in REDEngine 3
- [1078] Geometry Clipmaps: Terrain Rendering Using Nested Regular Grids
- [1605] SIGGRAPH REndering MAssive Virtual Worlds Course
	-https://cesiumjs.org/hosted-apps/massiveworlds/
- [324] Binary Triangle Trees for Terrain Tile INdex Buffer Generation
	-https://www.gamasutra.com/view/feature/130171/binary_triangle_trees_for_terrain_.php
- [670] Real Time Cloud Rendering
- [1720] Continuous Distance Dependant Level of Detail for Rendering Heightmaps