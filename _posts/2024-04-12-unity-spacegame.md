---
layout: post
title: 3D spacegame in Unity
categories: ["graphics"]
---
# 3D spacegame in Unity
---
<div class="post-date">
    <span>{{page.date | date: "%Y.%m.%d" }}</span>
</div>
<div class="intro">
This project is a 3D action space game that I made in Unity. I wanted to create a vehicle controlled type of game, and space environments are always awesome to me, so I decided to make a basic space game with 3 various levels where the player controls a spaceship. The focus of this project was making a good working game, meaning making all technical/functional mechanics work well. Note that the visual aspect was not the focus for me, and that almost all assets were collected. I did of course still try to make it look somewhat decent. In the post I will go in depth on all the elements.
</div>

<ul class="tags">
    <li>Unity 3D</li>
    <li>C#</li>
</ul>


## Gameplay footage
<iframe width="736" height="413" src="https://www.youtube.com/embed/PqNvRW4-uo8?si=c1sY6AmwarQkq6Zz" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen style="padding-top:10px"></iframe>

## Technical components
The mechanics I developed for this game were:
<ul>
    <li>
        <b>Ship movement:</b>
            using Unity’s physics system. The ship can move with the awsd keys and moves in the direction where the mouse is pointing. The q and e can be used to also rotate(roll) the ship.
        </li>
    <li>
        <b>Laser projectile combat: </b>
        Lasers are shot to the point where the crosshair is pointing at the moment of firing. This done by vector calculations, moving the laser vector to the point, with a set speed, for a set time. This works the same for the player and the AI.
    </li>
    <li>
        <b>Meteors</b>
        In space meteors are randomly generated across the space with random variable sizes, that rotate on their spot. The player can shoot them and destroy them, or bump into them which destroy and causes damage. They also have a function to move towards a given target point, and do damage to any damageable object. This is for example used to create a meteor storm in a level where they move towards an object that can be damaged, that the player needs to protect.
    </li>
    <li>
        <b>Health and damage system</b>
        Players and AIs have are instatiated with health points, and can take damage by colliding with lasers, or meteors.
    </li>
    <li>
        <b>Basic AI enemy ships </b>
        The enemy ships can persue the player, and can shoot lasers to try and kill the player if they're close enough. The player persueing mechanism was is a bit simple, so it does look a bit odd at times.
    </li>
    <li>
        <b>Game management</b>
        Navigation of main, level, pause menus. Settings passing/failing levels conditions and handle transitions of levels based on that. Handle audio effects and background music.
    </li>
</ul>


## Visuals
Like said before, this aspect was not the focus of this project. All the assets I just collected from various places, aside from one or two particles that I made myself. I just made sure that all the assets could fit together to get to a cohesive looking game.


## Conlusion
It took me some time to complete as my time to work on it was restricted, as I was doing it beside a full time coding job. I’m content with the end result, as I accomplished my goal of making a decent functional prototype 3D space game. I had already made some small 2D games with Unity, but not a proper 3D one yet. There are of course things that aren’t perfectly refined as can be seen in the video, but that’s out of scope as it’s not something I was to ship out. <br/><br/>
I definitely learned a lot from this project and got better in a lot of aspects: implementing basic mechanics (combat, movement, health) in 3D, management of the game (levels, music, menus, transitioning), and application of 3D math(vector math).


