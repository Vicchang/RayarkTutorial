# Computer Graphics
---
* ## Explain real-time rendering pipeline.
  Real-time rendering pipeline is a series of procedures to transform to 3D objects to 2D device view. There are many and many steps and we can divide them
  into three major process, application, geometry and rasterization.
  
  ![Rendering Pipeline](https://github.com/Vicchang/RayarkTutorial/blob/master/RenderingPipeline.jpg)
  
  * Application
  
	This process is to handle physical logics, including object movement, collision detection, calculation, algorithm and ...etc. Takng unity as example,
	all the C# sharp script and gameobject construction are happeneed here.
	
  * Geometry
  
	This process has many steps inside. The goal of this process is to transform 3D object to rasterization data and let pixel shader to draw on device screen.
	The process includes following steps, model transformation, camera transformation, lighting, projection transformation, clipping, and window-view transformation.
	
  ![Geometry Diagram](https://github.com/Vicchang/RayarkTutorial/blob/master/GeometryDiagram.jpg)
  
  * Rasterization
  
	This process focuses on pixel drawing. With the data from geometry, pixel shader can draw coloer by light, normal, tagent and texture.
  
* ## Explain how vertex shader and pixel shader works.
  * Vertex Shader
  
	Takes a single vertex and can adjust it, for example, transformation and calculation but not create new points/vertices. Vertex shader can be used to work out complex 
	vertex lighting calculation as a setup for the next stage and/or warp the points around (wobble, scale, etc).
	
  * Pixel Shader
  
	Calculates the colour of a pixel on the screen based on what the vertex shader passes in, bound textures and user-added data. This cannot read the current screen at all, 
	just work out what colour/transparency that pixel should be for the current primitive.	
	
* ## Explain Blinn-Phong shading.
  Blinn-Phong shading is a algorithm for simulating light reflection. People needs to simulate light in order to make objects in screen more realistic.
  Hence, Phone model was invented. It can highly simulate the light and let object more natural. The idea of phong model is to calculate the reflextion
  vector of light and product with object normal so as to calculate the percentage of colour brightness. However, the effor of calculating the 
  reflextion vector is high. Blinn-Phong shading was addressed. Blinn-Phong takes half way vector to represent the light reflextion vector and to calculate
  the percentage of coluor brightness. There are three difference bewteen Phone and Blinn-Phong.
  
  1. Blinn-Phong has less performance impact than Phong.
  2. Blinn-Phong is introduced to handle the issue of negative production vector of light reflection vector and eye view in Phong model.
  3. Phong is more realistic than Blinn-Phong.
  
* ## Explain forward rendering path and deferred rendering path.
  Both foward and deffered rendering path are here to define the rendering pipeline. As mentioned above, there are many procedures in rendering pipeline.
  Different procedures can have different effect and performance. The difference between two of them is the handling of light. Here are some details.
  * Foward Rendering Path
	1. Limit light source to 4 lights. 
	2. The rest of the lights is handled by SH, which has less performance effect.
	3. Can render transparent objects.
	4. Due to less of light source, the object is less realistic.
  * Deffered Rendering Path
	1. Unlimited light source.
	2. Can have full controll of lights.
	3. More reference to light cause more performance impact.
	4. Object display is more real.
	5. Can't render transparent objects.
	6. Lack of support anti-aliasing.
  
* ## Explain texture compress and its benefit. Why JPEG is not adopted when compressing textures? 
  > Texture compression is a specialized form of image compression designed for storing texture maps in 3D computer graphics rendering systems
  The benefit of it is to reducing overall bandwidth and storage needs throughout the graphics system.
  
  The reason why JPEG is not adopted is that the characteristic of texture compress and JPEG is different though the goal, reducing storage is the same.
  Texture compression focuses on decode speed, encode speed and random access. However, JPEG focuses on compression size. JPEG produces a variable 
  compression ratio, which is differed from different parts of the JPEG file. Hence, it is hard to do the ramdom access as texture compress.  
  
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