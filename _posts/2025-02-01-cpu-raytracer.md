---
layout: post
title: CPU multithreaded Raytracer
categories: ["front_page", "graphics"]
---
# CPU based Raytracer with multithreading
---
<div class="post-date">
    <span>{{page.date | date: "%Y.%m.%d" }}</span>
</div>
<div class="intro">
To learn more about ray tracers I followed the notorious guide of building a ray tracer in a weekend, which shows how to build a ray tracer from scratch with spheres as objects using the CPU. I’ve implemented multithreading to speed up the rendering process. I’ve also implemented triangles geometries.
</div>
<ul class="tags">
    <li>C++</li>
    <li>raytracing</li>
</ul>

![raytracer image]({{site.url}}/assets/images/cpu-raytracer/renderedImage.png)

The triangles look a little bit odd due to reflections not being fully correct and defocusing, but note that it's just a base I’ll be working further on a lot more.

## Implementing triangle ray intersections
As spheres were the only geometry I had, I wanted to implement ray triangle intersection to later load meshes and models. So I implemented ray triangle intersection using the ‘Moller-Trumbore’ algorithm which is documented in a paper called “Fast, Minimum Storage/Ray Triangle intersection” and is one of the commonly used methods. In short it consists of solving a system of linear equations based on the barycentric coordinates a triangle, to see if the intersection takes place inside within the triangle.

## Speeding up the process
Using a lot of samples per pixel and deeper depths for the rays can make things a lot slower. As this is a CPU program, one way to speed it up is using multi threading. It also doesn’t take too much effort to implement. It requires some refactoring to get to a function that one thread can pick up to process rows of the image. To equally divide the work over the threads, I just divide the rows of the image by the amount of workers, so each worker processes a given range of rows of the image. 

|samples per pixel|threads|rendertime(seconds)|
|:---|:---|:---|:---|
|100|1|488|
|100|16|75|

Rendering about 400 spheres takes 488 seconds on a single thread but 75 with 16 cores(8 physical cores) which is about 6.5 times faster. It’s not great, not terrible, but still a lot better than the starting case for not all too much effort. In a next iteration I want to optimize it using an acceleration structure like a BHV which is the more common approach, but I wanted to try the less common method first to see how it will compares.

## Debugging
It can be pretty hard to debug a ray tracer to find certain behavior. I had issues with normals pointing in the wrong direction, the closest intersection point not being taken, and more when implementing the triangles. Things that helped me were debugging by visualizing useful data like the depth and the normals like shown below.

![visualising distance and normals]({{site.url}}/assets/images/cpu-raytracer/DebugDistanceNormals.png)

Another approach is by inspecting a pixel that’s being rendered incorrectly to see whether a ray doesn’t self intersect or scatters and doesn’t show any unexpected behavior. It still remains pretty tedious and is something I will have to find better ways for.

<hr/>
I still want to implement other things like optimization with BVH, textures, and lighting among other things which I'll be doing on top of this, or in a whole new implementation alltogether. All in all the RT weekend has been a really good starting point to get warmed up to it again, and I’m definitely excited to take it further from here.
