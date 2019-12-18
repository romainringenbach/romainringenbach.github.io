---
layout: page
img: /assets/sblorbkiller.png
description: A game made during MetroidVania Month 5 game jam about saving a automated space station from Sblorbs
links:
  - name : "Source code"
    url : https://github.com/nealith/SblorbKiller
  - name : "Demo"
    url : https://nealith.itch.io/sblorbkiller
title: SblorbKiller
permalink: /sblorbkiller.html
order : 2
---


# Introduction

![illustration](https://img.itch.zone/aW1hZ2UvNDgyOTI4LzI3OTcyNzEucG5n/original/9SU2D%2F.png)

*You are a space technician sent to far away automated space station.  Once there, the station is really quiet, systems seem off.  You will discover very quickly the cause.*

This game is a personal project made in one week for the [Bored Pixels Jam 5](https://itch.io/jam/metroidvania-month-5) with the engine [Godot](https://godotengine.org/).

# Making level creation efficient

This game jam was the opportunity to learn and use tilemap in Godot. In this game you have different elements you can interact with : air traps, doors, screens, all via command panels.

Coding all this interaction for each element doesn't make sens. Unfortunately, the tilemap doesn't allow you to link specific tile to scenes (game object in Unity). Also, the tilemap has only one layer. So a create a node that is in charge of automating the creation of all interaction.

One the first things was to detect command panels (using the tile type). If they are adjacent to a door, a screen or an air trap, they toggle it on user action. I also can register a pair (command panel, door) to toggle door that are not adjacent.

For air trap, the player can use them to be teleported from a point to another, I have to link them to another air trap. So to automate it, I use another tilemap, with square colored tile. To link two trap, I put on them the same tile type.

I also created a mob spawner using another tilemap, so I just to place tile where I want a monster.

Here a visual of level building after 250 lines of code:

![example](https://img.itch.zone/aW1hZ2UvNDgyOTI4LzI3OTcyNzIucG5n/original/LN7EmT.png)

This approach allowed me to make my entire demo in less one day.

# AI of Sblorbs

The AI of Sblorbs is quite simple. Godot have a pathfinder system that allow to create area on tiles that are passable. The basic monster behavior is to move from a point A to a point B. The destination is a random point on the passable area (of all tiles that he can move on). But some Sblorb's behavior is to kill the player if he is in its area. Each *delta t* the Sblorb look for a new path to the player if reachable, else a random point.
