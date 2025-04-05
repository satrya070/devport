---
layout: post
title: Hague height rendering openGL
categories: ["front_page", "graphics"]
tags: ["C++", "openGL", "Python", "tesselation"]
num_tags: 3
---
# Rendering heightmap of the Hague in openGL
---
<div class="post-date">
    <span>{{page.date | date: "%Y.%m.%d" }}</span>
</div>
<div class="intro">
In the past I had worked with height data of the Netherlands (data source: Actueel Hoogtebestand Nederland), which was always pretty good open data. What I would do was extract heights from it for certain buildings, which is just a simple number. What I always wanted to do however was visualise it in some kind of 3D environment. To do that openGL is just perfect for that. I will explain how I went about it, and some optimizations I applied using tessellation. See below what it looks like. Find the repository <a href="https://github.com/satrya070/HeightRendererOG" target="_blank">here</a>.
</div>
<ul class="tags">
    {% for tag in page.tags %}
        <li>{{tag}}</li>
    {% endfor %}
</ul>

<iframe src="https://drive.google.com/file/d/15BBh4c1shRUvqCSEBdFCwi17raC7aOh-/preview" width="736" height="530" allow="autoplay" allow="fullscreen"></iframe>

## Getting the heightmap
The height data is freely available at the AHN(Actueel Hoogtebestand Nederland) website, and their most detailed data presents a height for every 0.5 meter which you can download in tiles of 5x6.25 kilometers. We’re actually lucky Holland has a lot of useful data available like this to the public. As I live in the Hague I of course am gonna do my city.

## Preprocessing heightdata with python
The first thing to do is process this heightmap a bit before, which I will do with Python. I first cropped out the map to contain the most iconic buildings of the city, taking a 1.5x1.5 km crop. You actually get 2 available files, the terrain height, and the full height meaning terrain plus the building. I subtracted the terrain height to have all buildings on a flat surface. See below a visual of the heightmap below.

![heightgrid of the hague]({{site.url}}/assets/images/heightRenderer/heightdata.png)

Note that some heights can be minus, as the measurement is being done relative to sea level. We can see from the figure the heights range between ~ `-5.0 - 150` which is correct as the highest tower in the city is close to 150 meters tall. Another thing is to make sure there are no NaN values, and to interpolate those. The last thing to do is to normalize this height range to a range of 255, to then be able to save it to a one channel PNG image that we can use to generate a heightmap in openGL.

## Drawing in opengl
For the initial approach I generated a triangle for each vertice with its neighbor vertices, which forms a triangle strip for each row. These can then be drawn with the `GL_TRIANGLE_STRIP` primitive. For the drawing all that needs to be done is to save the number of indices to draw per strip which is a simple calculation. The height of a vertice is contained in its y coord, which is then used in the fragment shader to give the vertice its color. The height is normalized between a value of 0-255 to in the image channel, so this gets calculated back to the actual range in the fragment shader (which is an easy calculation as long as you know the range of the actual heights). The higher the height, the brighter color it will be. While this works well and is the most simple and straightforward way to do it, it’s not very efficient as it takes up a lot of memory for what it is, and all processing of the vertices is done on the cpu in this method. The processing is also intensive with O(n2) complexity as it uses nested loops, and is just hard coding it for everything. There is also the draw call that’s called for every row, to draw a triangle strip.

## Implementing tessellation
To optimize it tessellation can be implemented to dynamically generate these vertices on the gpu, instead of processing every vertice individually on the cpu. The idea is to divide the xz ground map/plane in patches, which can then be passed to the tesselation shader that will subdivide the patches into triangles of a configured amount. I’ve set the patches to be 60x60(equals 30x30 meter) and the tessellation level to 64 that results in quads that will have lengths of 0.46875 meter (3000 / 50 / 64 * 0.5m), which is similar enough to the original quads that had a length that equalled 0.5 meter. Note that the height coordinate doesn’t get set here, but gets fetched later in the pipeline. This all gets set in the tessellation control shader. The generated vertices are then created by the tessellation primitive generator steps, and passed on to the tessellation evaluation shader. This is where I assign the exact coordinates of the generated vertices. The evaluation shader contains the xy ground coordinates of the patch, but the tessellation generated vertices are given as normalized values between 0-1 that need to be converted to the real xz coordinates. This is done with bilinear interpolation(see image below):

![bilinear interpolation]({{site.url}}/assets/images/heightRenderer/p1-1.webp)

After the above I now have all the actual xz coordinates of the generated vertices. As all these vertices are generated, I couldn’t just simply directly include the height value in the y coord like in the initial approach. So instead the height map is just included as a texture in this approach. All the patches were divided based on the width and height of this height image, so an xz coordinate directly corresponds to the same exact place on the height image. So getting the height of a pixel is just a simple matter of fetching the y coord of the xz location like so` texture(heightMap, texCoord).y` (texCoord being a generated vertice). The fragment shader remains unchanged as it has the exact same data as the initial approach.

![original vs tesselation]({{site.url}}/assets/images/heightRenderer/comparison.png)

See the image below for a comparison between the vertices as they are and the vertices done by tessellation. A little bit of quality gets lost in the detailed areas, as the edges and areas with frequent sharp curves are more smoothed out. However overall I think it’s very acceptable as I wouldn’t notice if it wasn’t known to me. Especially in terrains like hills and mountains, and with the texture instead of the wireframe, it wouldn’t be noticeable, where it is more commonly used for.

## Dynamic level of detail
Another optimization on top of this that can be done is to have a dynamic level of detail(LoD). The concept is to set different tessellation levels depending on how close or far something is from the camera. For anything that’s close you’d use a high level of tessellation, and low tessellation for things that are far. To do this I get a normalized 0-1 value of the distance value, z, of a vertice. Where 0 means the closest z/beginning of the projection view frustum, and 1 is the end of the frustum. This normalized value is then used to get the corresponding tessellation level between the set minimum of 16 and set maximum of 64. A vertice which sits in the middle of the z-axis of the frustum would have a normalized distance value of 0.5, which will have a tessellation level of 40 for example(16+(64-16) * 0.5 = 40). See how dynamic tessellation looks below:

<iframe src="https://drive.google.com/file/d/1ARhm7zvdkvSDP9nF5wnsiEimjZ6dsdSS/preview" width="736" height="415" allow="autoplay" allow="fullscreen"></iframe>

Note: I’ve exaggerated the difference between tesselation levels here to show the dynamic changing of detail level.

<hr/>
I hadn’t done anything with tessellation before, in the context of graphics API’s at least, so this was a project that I learned a lot from, specifically in getting to know tessellation on a detailed level and its place in the graphics pipeline in general. Plus getting to see and interact with my city this way is definitely awesome. In the future I hope to come back to this and port this to the web or something like that. I would also be interested in seeing if I can generate some kind of 3d model from it, to act as a base to build something further upon that’s cool. I hope the content has explained the process clearly enough and was interesting. For questions you can always contact me via my github.
<hr/>