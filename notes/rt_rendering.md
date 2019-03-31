# Real Time Rendering Notes

## Chapter 4: Transforms
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