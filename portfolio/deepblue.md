---
layout: page
img: /assets/deepblue.png
description: A student project that take place in anxiety-provoking atmosphere where you pilot a sub marine in the deep sea.
links:
  - name : "Source code"
    url : https://github.com/nealith/DeepBlue
  - name : "Demo"
    url : https://nealith.itch.io/deepblue
title: DeepBlue
permalink: /deepblue.html
order : 1
---

# Introduction

After the collapsing of the world and the rising waters, survivor of the humanity are force to sail on infinity seas. To survive and develop their fleet, people try to recover material from the past world by piloting submarine in deep sea. In these depths, the light disappears, the pressure crack the hull, and some terrible animals prowl. You can only count on sonars. A lot of pilots are plagued by strong anxiety. Will you be enough brave to explore this world?

For our final year at École Supérieure d'Ingénieurs de Rennes, we decided with [fonspa](https://github.com/fonspa) and [Hyanaki](https://github.com/Hyanaki) to develop a anxiety-provoking atmosphere video game.

For this project, we used [Godot](https://godotengine.org/) as engine.

This page is about my work on this video game.

# 3D Assets of the cockpit and animations

![cockpit](/images/deepblue/cockpit.png)

I realized all mesh needed for the cockpit with [Blender](https://www.blender.org/). I also made all the animations of the cockpit's elements.

Note: I didn't work on the textures, only on the mesh.

# Physics of the submarine

The submarine is a rigid body with a capsule shape. Another rigid body is added with a sphere shape under the submarine. Both are linked by a joint with no degrees of freedom. This setting will give to the submarine the behavior of floats (like in fishing) : the submarine will always stabilize itself. The submarine move by applying force and torque impulse.

# Active and Passive Sonars

![active_and_passive_sonars](https://img.itch.zone/aW1hZ2UvNTI5MTk5LzI3NTY2OTIucG5n/original/f%2BqOO5.png)

Both of the sonar are rendered as a texture and passed to the cockpit's screens via viewport.

## Active sonar

![active_sonar](/images/deepblue/active_sonar.png)

The idea is to create something that the player can use to move with caution when the brightness is too low and the field of view is not enough far.

To generate the texture, the sonar compiles the texture from two viewport, bot associated with a camera. These cameras are setup with an environment that lighting elements that match a certain mask (a 3D area with a spherical shape registers them). Both of there are also stabilized, one always look to the top of the scene and is placed under the submarine, the other does the invert things.

The two views are merged with a little difference of color in green levels to give more information to the player. The final view is added on a sonar framework.

## Passive sonar

To create the passive sonar, we use a 3D area with a spherical shape to compile all objects in a certain distance. The value of each object is determinated by the maximum between their movement (since the last check) and minimal value, divided by the distance to the submarine.

The value is registered in a texture of one line of pixels. At each check, the index is incremented. The texture is transformed showed as a graph by a shader.

# Specials effects on the submarine window

In another course, Special Effects, we have to create something with shaders/particles. To save time, we have been allowed to make them in our video game project.

On the windows of the submarine, there are two special effets. Both of the following effects are programmed in a shader.

- The first attempts to recreate far away dust cloud or shoal of fish
- The second is a cracking effect that appear in case of collision or attack from a big fish.

## Dust cloud effect

The idea was to add a shader on the window of the submarine (wich is a demi-sphere) to create an effect of dust cloud moving underwater, or of a faraway shoal of fish without having to use particles for that.


After browsing examples of shader, I found this Lava Lamp shader from [The Book of Shader](https://thebookofshaders.com/edit.php#11/lava-lamp.frag) could give an interesting look but it has two problems:

- It's doesn't apply correctly to the demi-sphere.
- The form it creates is binary and does not give the expected look (We need something with more blurs and transparency).

To solve the first problem, I merged this shader with the viewport technic presented in this tutorial from Godot [tutorial from Godot](https://docs.godotengine.org/fi/latest/tutorials/viewports/using_viewport_as_texture.html).

Also this tutorial present another noise function adapted to 3D rendering by [Inigo Quilez](https://www.shadertoy.com/view/Xsl3Dl). So the original noise function has been replaced by this one.

Once the 3D rendering noise and the 3D algorithm used to create a texture that is suited for a sphere have been merged, I had to adapt the lava lamp effect algorithm for 3D (the original algorithm only takes in count two spacial components.). This change is simply made by adding a third value in the parameter given to the new noise function. This third value is the length of the vector X,Y, themselves given as the two first parameters.

By changing some arbitrary value in the algorithm, we get some blurred transparent form moving slowly.

![dome_effects](/images/deepblue/dome_effects.png)

## Rift effet

![rift_effect](https://img.itch.zone/aW1hZ2UvNTI5MTk5LzI3NTY2OTUucG5n/original/8sp1fb.png)

The rift effect is base on a handmade texture (to save time). One other solution would have been to develop another shader that use noise functions to create the rift. But this special effect is associated with another effect, a water leak (particles). So the rift has to be determinated.

Globally, this shader just reveals the texture pixel by pixel. They are two conditions to be revealed:

- Begin under a certain distance from the rift origin.
- Having at least one neighbor revealed.

So theses conditions create a natural looking rift creation with time. The only default is that the rift is revealed in 3 times (the 3 lifes of the submarine), that make the end of the rift an each time weird looking (should be sharp, but it's more rectangular).

The ideal solution could be merging the texture technic to have a main determinated form for the rift (needed for the water leak effect) and using a propagation algorithm to make it more realistic.

# Particles emission

We use particle emission for 3 things in this game.

- Near floating dust and other particle
- Floating particles/dust that reinforce the lighting of the submarine
- A water leak when the submarine is broken, just at the end of the window's rift (I didn't make it, so it will not be detailed here)

## Near floating particle

![floating_dust](/images/deepblue/floating_dust.png)

When I played [Soma](http://www.somagame.com/), I was impressed by underwater scenes. One of the things was the floating particles of dust. Here is my attempt to recreate that.

I got good settings for particles emission, but I need something else for the shape than spheres. I decided to solve this problem by a shader. This one is based on this [deformation shader](https://digitalerr0r.net/2012/03/03/xna-4-0-shader-programming-5deform-shader/) wich is vertex shader. There are some adjustments to make:

- The transformation is the same for all spheres.
- The transformation creates some form that are not suited for our visuals expectations.

The first problem is solved by using in the algorithm the supposed final positon of each vertex of the sphere. The second is performed by modifying the different arbitraries values on the algorithm. Also, some elements have been removed because they didn't relly impact the result.

## Particles system that reinforce lighting

![spots](/images/deepblue/spots.png)

The goal is to get a better impact on the effect of the submarine's light spots. Like having a visual cone of the light. For this one, sphere are enough. The particle emission is setup to having a ton of really small spheres that are moving fast in a whirlwind from the spot. By using the masks, the sphere are only enlightened by the spots giving us the expected result.

![spots_in_game](https://img.itch.zone/aW1hZ2UvNTI5MTk5LzI3NTY2OTQucG5n/original/VPuhZv.png)
