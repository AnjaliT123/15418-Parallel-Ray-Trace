# 15418-Parallel-Ray-Trace
Implement parallel ray tracing in CUDA, based off code in Scotty3D 

## Project: Parallel Ray Tracing in CUDA and OpenMP
Group members: Anjali Thontakudi, Winston Cheung

## Summary
We are planning to implement parallel ray tracing in CUDA, starting with a working version in openMP. In order to render more complex scenes, we would also like to implement BVHs in CUDA and openMP.

## Background
Our goal here is to parallelize ray tracing. In ray tracing, we trace a ray from the camera lens into the scene we're trying to render. As we trace the ray backwards, we detect collisions with the scene and use Monte Carlo integration to get the value of the pixel from different light sources. After detecting this collision, we can then trace the ray farther back into the scene to simulate light bouncing off of different objects and stop when the ray hits a maximum depth. 

![alt text](https://github.com/AnjaliT123/15418-Parallel-Ray-Trace/blob/c571e683c76f8a4c3c326f61e3531a7cd6876523/raytrace_pics/Ray%20Tracing%20Diagram.png)


To speed up rendering, we can use Bounding Volume Hierarchies (or BVHs), which split up the space into boxes. This is similar to the Barnes-Hut algorithm but in 3D: we create more boxes in regions where there are more primitives (objects) in the scene. When we trace rays, instead of checking collisions with all primitives in the scene, we instead check if the ray has collided with a bounding box. If so, we can recursively check the boxes within the box, until we finally reach a subset of primitives in the scene. 

![alt text](https://github.com/AnjaliT123/15418-Parallel-Ray-Trace/blob/c571e683c76f8a4c3c326f61e3531a7cd6876523/raytrace_pics/BVH%20Diagram.png)

We can parallelize over rays here, which will allow us to detect more collisions at once and render pixels faster. We can also parallelize the BVH creation, by creating bounding boxes that are in the same level in the hierarchy at once. This preprocessing will allow us to speedup rendering further. 

## Challenges
* In order to render images well, we need to sample many rays, which will often travel in very different trajectories. We'll have to figure out how to deal with divergence when porting our code over to CUDA so we utilize thread warps effectively 
* Bouncing the rays across different parts of the scene is a recursive, sequential process, which may slow down computation depending on how we set the max depth for our rays. We'll need to figure out a way to work around these dependencies, or eliminate them from the pipeline. 
* Generating the BVH is also a recursive process, since we decide where to split the higher level BVHs based on the concentration of objects in different parts of the scene. We'll have to figure out how to reduce these dependencies, and possibly modify our BVH structure so we don't have hierarchies with high depth

## Resources
We'll be using the Gates lateday machines and PSC bridges computers to test the openMP version of our project, and the NVIDIA geForce RTX GPUs on the lateday machines for the CUDA version of this project. 

We'll be using starter code from the 15462 implementation of BVHs and ray tracing, which follows the algorithms specified in the [Scotty3D](https://cmu-graphics.github.io/Scotty3D/) repo. We could also refer to this [NVIDIA article](https://developer.nvidia.com/blog/thinking-parallel-part-ii-tree-traversal-gpu/) to port the BVH creation and traversal code to CUDA. 

## Goals and Deliverables

### Plan to Achieve
* Fully implemented parallelization of ray tracing in both openMP and CUDA, which can be used to render scenes in Scotty3D. We would expect reasonable speedup from openMP and significant speedup from CUDA, since we have so many rays to trace and can maximize the parallelism in both. 
* Fully implemented BVH construction and traversal in openMP. We expect reasonable speedup here for similar reasons. 
* Speedup comparisons of openMP, CUDA, and sequential Scotty3D versions of ray tracing (with and without BVH for openMP)

### Hope to Achieve
* BVH implementation in CUDA 
* Speedup comparisons of ray tracing with BVH in CUDA 

### Alternate Approaches
If CUDA proves to be too difficult, we can instead try implementing the code in MPI, since this is much closer to the C++ we're used to working with. We would provide speedup comparisons between the sequential and MPI versions in this case. 


## Platform Choice
It's ideal for us to start by using openMP so we can ease into thinking about ray tracing in parallel before diving into the CUDA implementation. It'll also give us an idea for which aspects of the code we can parallelize over to increase speedup the most. Progressing to CUDA and using GPUs makes sense since we'll be have to trace a large amount of rays and build complex bounding box hierarchies, which will allow us to utilize the parallelism of the machines more fully. It's also natural to use GPUs for a graphics application. 


## Schedule
* Week 1: Re-familiarize with Scotty3D implementation of Ray Tracing and BVH construction. Use openMP to parallelize over rays without bounding boxes, analyzing speedup times
* Week 2:  Implement BVHs in openMP, complete associated comparisons
* Week 3: Start implementing ray tracing in CUDA and prepare checkpoint documents
* Week 4: Finish ray tracing implementation in CUDA and compare with openMP and sequential versions
* Week 5: Start implementing BVH in CUDA 
* Week 6: Finish implementing BVH in CUDA, do associated comparisons and complete final reports.  


