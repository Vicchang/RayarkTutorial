# Computer Graphics
---
* ## Explain real-time rendering pipeline.
  Real-time rendering pipeline is a series of procedures to transform to 3D objects to 2D device view. There are many and many steps and we can divide them
  into three major process, application, geometry and rasterization.
  
  ![Rendering Pipeline](https://github.com/Vicchang/RayarkTutorial/blob/master/RenderingPipeline.jpg)
  
  * Application:
	This process is to handle physical logics, including object movement, collision detection, calculation, algorithm and ...etc. Takng unity as example,
	all the C# sharp script and gameobject construction are happeneed here.
	
  * Geometry:
	This process has many steps inside. The goal of this process is to transform 3D object to rasterization data and let pixel shader to draw on device screen.
	The process includes following steps, model transformation, camera transformation, lighting, projection transformation, clipping, and window-view transformation.
	
  ![Geometry Diagram](https://github.com/Vicchang/RayarkTutorial/blob/master/GeometryDiagram.jpg)
  
  * Rasterization:
	This process focuses on pixel drawing. With the data from geometry, pixel shader can draw coloer by light, normal, tagent and texture.
  
* ## Explain how vertex shader and pixel shader works.
  * Vertex Shader
	Takes a single vertex and can adjust it, for example, transformation and calculation but not create new points/vertices. Vertex shader can be used to work out complex 
	vertex lighting calculation as a setup for the next stage and/or warp the points around (wobble, scale, etc).
	
  * Pixel Shader
	Calculates the colour of a pixel on the screen based on what the vertex shader passes in, bound textures and user-added data. This cannot read the current screen at all, 
	just work out what colour/transparency that pixel should be for the current primitive.	
	
* ## Explain Blinn-Phong shading.
* ## Explain forward rendering path and deferred rendering path.
* ## Explain texture compress and its benefit. Why JPEG is not adopted when compressing textures?
* ## How does ParticleSystem work?
  A particle system is to manage lots of particles. A particle system contains a emitter and lots lots of particles. A particle has following attributes.
  1. Lifespan
  2. Similiar to gameobject but more lightweight
  3. All particles rendered at the same time
  4. Can move
  
  Basically, you can image that you create 20 similar small gameobjects. Each object has its routine and lifespan. You can write a management class, "ObjectManager". ObjectManager keeps tracking
  all the gameobjects and updates their position and lifespan. This is kind of a small particle system. However, here comes the story. What if you need one thousand of the similar small gameobjects.
  It is frustrated. Needless to say, we may need millions of objects. A particle system is here to solve the problem. People may ask that why do we need so many particles in computer graphic?
  It is all about reality. The world is contructed by many small particles, which is small enough that we can barely see. However, it is due to those small particles that the world is so
  real. People in computer graphic want to let this reality into our device or monitor. That is the reason why they need so many particles.

  A particle system is a really complicated system. So far, we know that it is constrcuted by lots lots of particles and a emitter. Here is something we need to be cautious about.  
  1. Every particle is a struct object in C#. You need to be careful about the memory usage, the copy and the reference.
  2. There are two render types, mesh and billboard, for particle system in unity. Mesh, 3D object, can simulate more visual effect but has lower performance. Billboard, 2D object, has less visual
  effect but higher performance.
  3. You can choose GPU instancing or CPU instancing in Unity. With GPU, the performacne would be really great. However, it requires high level GPU.
  
* ## How does Skinned Mesh work?
  > Skinned Mesh is designed to deform mesh in order to create animation. Typically, skinned mesh is come up with skeleton animation.
  While vertex shader stage, we can transform vertices to other positions. Usually, we transfrom vertices from object coordinate to world coordinate, camera coordinate and device coordinate.
  After that, we then Rasters the vertices and then do pixel shader. With skinned mesh, we now add one more step between object coordinate transformation to world coordinate transformation.
  Bone offset is added to each specific vetices. Hence, after transforming to world coordinata, it is not the position of vetex on mesh but the position of vetex on bone. With this skill,
  as long as you move the bones, the mesh or the vertices would move, too. This is why we say skinned mesh is relative to skeleton animation. 
* ## Training Project