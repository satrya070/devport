---
layout: post
title: Raycasting with Pygame
categories: ["front_page", "graphics"]
---
# Raycasting
---
<div class="post-date">
    <span>{{page.date | date: "%Y.%m.%d" }}</span>
</div>
<div class="intro">
Raycasting is a cool technique in game development that was used to create some of the first ‘3D’ games like Wolfenstein. It’s relatively easy to build a basic 3D environment like that, and Pygame is good tool to build it with. Pygame is a code only engine and more barebone than a game engine like Unity, so you’ll get an good in-depth understanding in how to implement Raycasting. As my experience with Pygame in specific is sparse, I used this video to help me build a basic implementation, and in this post I’ll dissect all key parts needed to get to a simple working Raycasting implementation. This was to reinforce my own understanding as well as for others who are learning it.
</div>

<ul class="tags">
    <li>Python</li>
    <li>Pygame</li>
    <li>raycasting</li>
</ul>

The repository with all the code can be found <a href="https://github.com/satrya070/raycasting" target="_blank">here</a>. The raycasting logic parts contain a lot comments to make it as clear as possible.

![raycasting]({{site.url}}/assets/images/raycasting/raycasting.gif)

## The program
Here is a diagram below of how the program looks like:

![raycasting_program]({{site.url}}/assets/images/raycasting/raycasting-program.svg)

It is a simple program as there are just 4 classes. The `Game` object will hold all classes. The `Map` class draws the map of wall blocks. The `Player`  class is a entity that can move, has a x and y position, and the angle of the direction it is looking at. It also has methods which take care of moving the the player and detecting collision on the walls to make sure it doesn't go through walls. The `RayCasting` class uses the data from the map and player entity to do all the calculations and 3D projections of the raycasting. The `settings` block is a file which basically contains all global base values like the width and height of the program, raycasting settings, and more.

## Pygame coordinate system
One important thing to mention first is the coordinate system of Pygame. The origin is top left, which mean the x-axis goes from left to right, and the y-axis goes from top to bottom. This also means that degrees are measured opposite of what you might expect. See how the coordinate system works in the illustration below, and notice what 45 degree is in this system. The width of our program in this case will also be 640x480.

![pygame_coordinate_system]({{site.url}}/assets/images/raycasting/coordinate_system.jpg){: style="width: 400px; margin: 20px 0;"}

## Raycasting calculations
The Raycasting technique starts with shooting out rays and calculating the distance for every object each ray hits. To explain I'll show how this calculation is done for a single ray. The raycasting object has as attribute a game object, from which every attribute of its map and player object is accessible. Now with this data we take the player position and shoot out a ray, for now lets just shoot out 1 ray in the direction it's looking. Conceptually this will look something like the illustration below.

![ray_calculation_1]({{site.url}}/assets/images/raycasting/ray_calculation_1.jpg){: style="width: 600px; margin: 20px 0;"}

The white dot represents the player, the line the ray, and the dark blocks are walls. The map is basically grid and grids can be set to be walls. In the illustration you can also see the verticals(green lines) and the horizontals(red lines) which will be essential in calculating the distance.

When we do a distance calculation for a ray, we set the limit to viewing 20 blocks ahead. For each ray there will be a calculation loop of max 20 iterations. If a ray finds/hits a wall before that, then the calculation is done for that ray, and then we can move to the next ray. In an iteration the next closest intersection of the ray with a vertical and horizontal gridline is calculated. Above you can see that we've done 2 iterations and that a wall was intersected, which means the distance calculation for that ray is done. The green and red dots on the ray line indicated where it has intersected with the vertical and horizontal gridline. The reason we calculate for both here is to get the exact distance to the wall. We see that the wall was reached in the second iteration, and that the vertical intersection was the one to intersect the wall (the horizontal line intersects passed the wall).

With some basic trigonometry and the maptile position, player position, and angle of the ray, we can do this calculation. Below I've made an illustration of how the calculations are done. These calculations can also be found in the code to get the best understanding.

![ray_calculation_2]({{site.url}}/assets/images/raycasting/ray_calculation_2.jpeg){: style="width: 700px; margin: 20px 0;"}

You can see that from the player position we first calculate the distance to the very next vertical and horizontal line. From then on we can keep incrementing the vertical and horizontal lines checks by 1 until we hit the limit (20 iterations) or have found a wall intersection.

### Rays cast

In the real thing there's of course numerous of rays casted. In this implementation the player is set with a field of view(FOV) of 60 degrees. Within this FOV 320 rays are cast.

![raycasts]({{site.url}}/assets/images/raycasting/rayscast.jpg){: style="width: 400; margin: 20px 0;"}

Below you can see how it looks in Pygame, and that every ray distance calculation stops when it reaches a wall. This is basically all the data that we'll need to create the 3D projection, which we'll do in the next section. In a game for example you could use this as a mini-map.

![raycasting_map]({{site.url}}/assets/images/raycasting/raycasting_map.gif){: style="width: 400; margin: 20px 0;"}

### Projection
I’ve set the `screen_distance` to 554 pixels, meaning the distance from the screen to the camera position. This screen is called the projection plane which ultimately renders what we will see. It’s just the distance or focal length you get from the triangle with a base of 320 (screen width / 2) that’s 30°(fov / 2), see the visual below. 

![screen_distance]({{site.url}}/assets/images/raycasting/screen_distance.jpg){: style="width: 100%; margin: 20px 0;"}
I’ve set the length of 1 unit or tile in the grid, equal to the screen_distance of 554 pixels. The projection of the height is done by dividing the `screen_distance` by the depth of the ray. This `height_projection` tells us how the height of the object is in pixels on the projection plane from that distance. To get where the start y coordinate(top) of an object, we take half of the screen(240) minus half of the `height_projection`. This will give the start of the y coordinate. With the top y coordinate known, we also know the bottom of the y coordinate by simply adding the height of the object to the top y coord. See the below figure to see how that looks.

![height_projection_examples]({{site.url}}/assets/images/raycasting/height_projections.png){: style="width: 100%; margin: 20px 0;"}

With a depth/distance of 1.154 for example the height would be equal to the viewport height. The object half height 240 (1.154 / 554 / 2 = 240), and the top y coordinate is the half height minus the viewport height minus which is 0 (240 - 240).


### Fisheye effect
At this point it works but not fully correct yet, as things are being projected with a fisheye effect. When standing directly in front of a block, the middle ray that's being casted is perfectly perpendicular to the camera and the object. However every other ray (slightly) isn’t, and will have depth that’s actually slightly longer, the longer the ray cast is, the “further” it will be projected on the plane, which causes this fisheye effect which you can see below.

![fisheye_effect]({{site.url}}/assets/images/raycasting/fisheye.png){: style="width: 100%; margin: 20px 0;"}

Luckily the fix is simply by just multiplying the length of the ray the cosine of the angle which means every ray its length gets cut to the same length. On the figure below on the right you see the top view where the rays get shortened with the cosine multiplication, causing them to get projected correctly.


![fisheye_correction]({{site.url}}/assets/images/raycasting/fisheye_correction.png){: style="width: 100%; margin: 20px 0;"}
<hr/>
And that’s all there is to building a simple raycaster in with Python. This is definitely a fun project that I’d recommend if you’re interested in projecting stuff in “3D” if you haven’t done it before. The implementation can be simple, doesn’t require too much hassle, and the math is just some normal trigonometry. I hope the read was interesting and for questions you can just hit me up on Github.