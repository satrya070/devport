---
layout: post
title: Raycasting with Pygame
---
# Raycasting
---

<div class="intro">
Raycasting is a cool technique in game development that was used to create some of the first ‘3D’ games like Wolfenstein. It’s relatively easy to build a basic 3D environment like that, and Pygame is good tool to build it with. Pygame is a code only engine and more barebone than a game engine like Unity, so you’ll get an good in-depth understanding in how to implement Raycasting. As my experience with Pygame in specific is sparse, I used this video to help me build a basic implementation, and in this post I’ll dissect all key parts needed to get to a simple working Raycasting implementation. This was to reinforce my own understanding as well as for others who are learning it.
</div>

The repository with all the code can be found [here](https://github.com/satrya070/raycasting). The raycasting logic parts contain a lot comments to make it as clear as possible.

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
For the projection of the height we determine a projection plane distance from a triangle that we make from half the distance of the width(320 pixels). See the illustration below how the this visually is determined. It's basically the distance we get from using a field of view of 60 degree(for the triangle we take the half which is 30), and calculating the distance we need to cover the full width which is: `320 / tan(30) = 554` pixels. With 320 rays being cast each ray projection will account for 2 pixels in the width on the projection plane, adding up to the 640 width we have set.


![screen_distance]({{site.url}}/assets/images/raycasting/screen_distance.jpg){: style="width: 100%; margin: 20px 0;"}

The projection of the height is done by dividing the `screen_distance` by the maptile distance. This `height_projection` tells us how high in pixels the object will be in the projection plane. To get the start position of the y coordinate, we take half of the height of the screen (240) minus the half of the `height_projection`. Below are examples of how it looks with a maptile distance of 1 and 1.154.

![height_projection_examples]({{site.url}}/assets/images/raycasting/height_projections.png){: style="width: 100%; margin: 20px 0;"}

For the first first above example we see how it would get projected with a distance of 1. As for the calculation of projecting the block lets go through the earlier defined calculations with these samples. We first get the heigh_projection by doing: `554 / 1 = 554`, so at a distance of 1 a block will just have have a height of 554 pixels. Then we calculate the the y starting position by doing half_height - half_projection_height, which is `240 - 277 = -37`. Notice how the block can't be fully projected in from this distance. In the samples next to it we have the minimal distance it takes to fully project a wall block on the screen. As the heigh_projection is `480 (554 / 1.154)` and the starting position is `0 (240 - 240)`

Note that the vertical FOV has a different angle with this calculation than the horizontal FOV. When projecting the blocks it will be a little wider then it is tall, which is the expected behaviour here. See below:

![screen_distance]({{site.url}}/assets/images/raycasting/height_fov.jpg){: style="width: 100%; margin: 20px 0;"}


### Fish eye correction
When we use the above height projection calculation it doesn't actually fully correctly projects everything yet. When we look at the wall line in the FOV, the distance from the wall line to the player can be different at any point on the wall basically. So when we the heigh projection calculation on a ray at the very left or right of the FOV, we end up with different projections than the ray which is perpendicular to the wall and has the shortest distance to the wall (the angle of the player). This difference causes the fisheye effect you see below where the further the wall point is from the player, the smaller the projections get.

In order to fix that we need the length of every ray to be as long as the ray that's directly perpendicular. This can be easily achieved by doing this calculation for every ray: <br>
`distance * cos(player.angle - ray.angle)`.
Below on the right you see how it looks when all projection points all have the same distance now. Now the projection of the wall is perfectly straight like we want. 

![fisheye_correction]({{site.url}}/assets/images/raycasting/fisheye_correction.png){: style="width: 100%; margin: 20px 0;"}

This pretty much documents everything already about this basic raycasting implementation. The calculations can take a little bit time to go through but in the end it's not all that much or hard. I hope it was helpful in any way or at least interesting to read about it.