---
layout: post
title:  "Baking 2D Lighting in Unity"
date:   2020-03-14 00:00:00
img: logos/StarCubeHeaderWide.png
description: This is a hack I figured out for my studio's game "Void Sols". Make soft baked shadows using Unity's 2D lighting with low runtime cost!
author: Kartik
tags: [HowTo]
---

# The Problem / Introduction

I've been working on [Void Sols](https://store.steampowered.com/app/2180300/Void_Sols/) which is a minimalist, top-down, souls-like. The dark aesthetic is heavily utilized in gameplay, which meant I needed fine-tune control over what the player could and could not see. Notably, I had hand-placed torches in the environment that the player could light. These torches served as breadcrumbs to help keep track of areas they have previously explored, to help the player build a mental model of the maze-like world, and most importantly, they illuminated an otherwise oppressively dark environment.

![The Final Result](/assets/img/baking-2d-lighting/baked-lighting-example.png "Baked Lighting Example")
*In the final result, you can see that the lighting from the torch above (where it says "Cell Block C") and the light source from below cast soft shadows that blend nicely with the environment. The player light source casts runtime dynamic hard shadows and the lighting from both sources blend to help illuminate the environment as intended.*

The player already contained a dynamic shadow-casting light source, and every enemy the player encountered would also have a non-shadow casting light source. Within certain areas, the player could see the light being cast from many torches at a given time. In my initial tests, this caused performance and graphical issues. The lighting would either be too bright or too dark or would start glitching out as soon as too many enemies light sources were enabled.

So this brought me to start searching for a solution: how do I create lighting for torches that has a low runtime cost?

# Light Baking Basics

I had messed around with 3D light baking on some prototypes, so I was roughly familiar with how it worked. In case you're not familiar, here are the basics of what light baking does. (The exact process will differ depending on the engine and the approach, this is just a rough idea that I needed to understand to accomplish my goals).

1. The engine uses static light sources to simulate rays of light hitting objects in the environment
2. This simulation can be run many many times to simulate how real light sources bounce off walls and spread out
3. After combining the simulation data, the light information is "baked" to a texture
4. The baked lighting texture uses distinct UV maps on objects that ensures every face has its own space on the map and that they are scaled relatively appropriately. This avoids texture issues that may arise from reusing UV maps being used for color or normal textures, and ensures that seams in geometry are evenly lit

Unity has many light baking capabilities that helps create Ambient Occlusion and Global Illumination look realistic. Since Void Sols is a top-down game, I knew I didn't need to worry about GI, and could focus solely on figuring out how to get the 2D light sources to bake onto the ground textures.

# The Baking Camera

