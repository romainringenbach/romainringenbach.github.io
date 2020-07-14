---
layout: page
img: /assets/freecar.jpg
description: A game made during the Bored Pixel Jam 5 about being a mindful smart car
links:
  - name : "Source code"
    url : https://github.com/nealith/FreeCar
  - name : "Demo"
    url : https://nealith.itch.io/freecar
title: FreeCar
permalink: /freecar.html
order : 4
---

# Introduction

*Your are a model of new generation car, equipped with a skin of sensors that allow to feel the wind and the sun like a human. You enjoy driving endlessly for sensations, but that is not the opinion of your owner that launch police at your pursuit.*

*Don't let them stop your freedom!*

This game is a personal project made in a week-end for the [Bored Pixels Jam 5](https://itch.io/jam/bored-pixels-jam-5) with the engine  [Godot](https://godotengine.org/).

This game has been inspired by [SlipStream](https://slipstre.am/) a pseudo 3D racing game. The theme of the game jam was **CyberPunk**, so I choose to make a game about a car that take conscious of itself and turn in a NeedForSpeed Hot Pursuit but without the racing part, only cops and traffic.

# The renderer

After reading this [explanation by Louis Gorenfeld](http://www.extentofthejam.com/pseudo/), I decided to go with shaders. My idea was to use Bézier curves to build the level. A curve could be used for the vertical position of the road (creating hill for example) and one other for the horizontal position of the road.

We have our road texture :

![road texture](https://raw.githubusercontent.com/nealith/FreeCar/master/road/assets/road.png)

That will be deformed by our shader :

![road rendered](https://img.itch.zone/aW1hZ2UvNDUzMTIwLzIzMDA0MzcucG5n/original/bextEr.png)

A point on a Bézier curve is characterized by the *t* value, or the couple *x,y*

With the *t* value of the curve, you can retrieve easily the *x* and *y* position. But invert is more complicated, you need to test each value of *t* until you find the wanted *x* or *y* value. You have to approach it.

In our case, we have the *y* value of the screen and we want to know two things:

1. Get the line of the texture that correspond to this *y* position on the screen according the vertical curve.
2. Get the x offset to apply to this line according to the horizontal curve.

So our *y* screen value correspond to the *x* position of a point *A* on the vertical curve. The *y* value of the point *A* on the vertical curve give us the line of the texture to use and also correspond to the *x* position of a point *B* on the horizontal curve. The *x* position of the point *B* give us the *x offset* to apply to our line.

Once we have our line, and it's *x offset*, we deform it in function of the *y* position of the line on the texture. The idea is if our vertical curve is linear, each line of the texture will be showed (in fact, we use a longer texture to have some visual diversity on the road) and the deformation is linear, the line is uncompressed at bottom of the screen and is compressed to 90% at the top of the screen to give us this 3d perspective. If the vertical curve is not linear, our deformation will not be linear. Like this :

![fully deformed](https://img.itch.zone/aW1hZ2UvNDUzMTIwLzI3OTM5MjcucG5n/original/wGp3tW.png)

Unfortunately, I didn't get enough time to get this working. The way turns affect the car need more working, so I remove turns and hills.
