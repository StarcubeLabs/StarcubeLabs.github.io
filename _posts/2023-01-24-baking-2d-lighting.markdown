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

I've been working on Void Sols (how do i link?) which is a minimalist, top-down, souls-like. The dark aesthetic is heavily utilized in gameplay, which meant I needed fine-tune control over what the player could and could not see. Notably, I had hand-placed torches in the environment that the player could light. These torches served as breadcrumbs to help keep track of areas they have previously explored, to help the player build a mental model of the maze-like world, and most importantly, they illuminated an otherwise oppressively dark environment.

TODO: (insert image of game, with torches)

The player already contained a dynamic shadow-casting light source, and every enemy the player encountered would also have a non-shadow casting light source. Within certain areas, the player could see the light being cast from many torches at a given time. In my initial tests, this caused performance and graphical issues. The lighting would either be too bright or too dark or would start glitching out as soon as too many enemies light sources were enabled.

So this brought me to start searching for a solution: how do I create lighting for torches that has a low runtime cost?

# Light Baking Concepts

I had messed around with 3D light baking on some prototypes, so I was roughly familiar with how it worked. In case you're not familiar, ...
