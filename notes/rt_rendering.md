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
```
1 0 0 Tx
0 1 0 Ty
0 0 1 Tz
0 0 0 1
```
#### Rotation
Rx(phi)
```
1 0 			0 			   	0
0 cos(phi) -sin(phi) 	0
0 sin(phi) cos(phi) 	0
0 0 			0 				1
```
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