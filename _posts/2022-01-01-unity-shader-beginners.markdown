---
layout: post
title:  "Unity Shaders - A Practical Translation (Part 1: The Basics)"
date:   2022-01-01 00:00:00
img: shader-beginners/header.png
description: Taking a look at Unity's HLSL shaders - explanations included.
author: Spex130
tags: [Unity, Breakdown, Shaders]
---

## Intro

This article takes a peek into what HLSL shaders in Unity3D are from a coder's perspective, and how to understand them enough to get a practical grasp on making them do what you want. This won't really teach you the hard low-level details, but you should be able to understand each section of a shader and why each part exists when you're done. So with that, let's get started!


## Part 1: Process of a Unity shader

<br>

### P1-A: Wait, what do renders pass anyways?

<br>

So, the first thing to learn about shaders is what shaders can do. There are _two types of information_ that (basic) Unity shaders can manipulate - that being the actual model data (the **Vertices**) and the resulting pixels from rendering that model data (the pixel **Fragments**). Shaders manipulate this information in two discrete steps, better known as **Render Passes**.

* The **Vert** pass is run **first**, and can:
  * Manipulate "physical" data points like vertex locations, vertex rotations, and other "hard" data about the model itself or its relation to the world around it.
  * Collect and store these data points to be used by *Render Passes* that come after it.
* The **Frag** pass is run **second**, and can:
  * Manipulate the colors and shading of the model's surface. This includes whether or not the model's shading is affected by lights, and whether or not the model is being colored by a Texture image or not.
  * Being able to manipulate a model's surface also includes things like alpha transparency, texture distortion, and faking false geometry using Normal Maps - among other things.
  * Data that was collected in the Vert phase can be used in the Frag phase if the user decides to pass it along. **It's not sent automatically, however - it must be set up manually.**

Shaders that only utilize the *Vert* pass are called *Vert Shaders*. Likewise Shaders that only utilize the *Frag* pass are called *Frag Shaders*. Shaders that only use a single one of these passes are rare however, most common shaders use *both*. And these shaders that use both are known as **Vert Frag Shaders**.

There is also a special *Unity-specific* 3rd type of shader that automatically combines the two known as a **Surface (or Surf) Shader**. The benefits here are that *the distinctions between the Vert and Frag passes are discarded*, and logic for both can be done within one pass. **Technically** both passes still are happening in order in the background though, so be aware that even in Surf Shaders **Vert logic always runs before Frag Logic, no matter what**.

<br>

### P1-B: Exactly how does a GPU calculate this stuff?

<br>

I know I said we weren't gonna get as low level as understanding GPU calculation here, but this bit is kind of important. Understanding everything after this is going to hinge on this very important concept. Are you ready? The key to understanding shader code is understanding that...

**It's all two FOR Loops.** 

Like, the whole thing.

Sound simple? Well, *that's because it is* actually. The Vert and Frag passes both are separate FOR Loops that run on two different arrays of data.
* The Vert pass is given an array of **every vertex on the model**. It runs first.
* The Frag pass is given **every pixel on the screen that is dedicated to rendering that model**. (Generally.) It runs second.

But wait? Doesn't that sound like a lot of data to be brute forcing through in a single loop per array? Well, you have to remember that GPUs are VERY good at their jobs. An AMD Radeon 7973 from 2012 could run 16 TeraFLOPS of Single-Precision caluclations handily. That's 16 TRILLION single-point calculations per second - and as far as standards go these days that graphics card is **a relic**. Your computer can handle it. *Don't worry*.

<br>

### P1-C: Beware the FOR Loops

<br>

What's important to understand here is that *these FOR Loops are inescapable*. You can't manipulate the data before it goes into the loop, and on a basic level you can't reach other data points in the array other than the one you are handed. It's like a library check-out system. Your pass is handed a data block from library. While you have it you can do WHATEVER you want to it, but once you're done it needs to go back in the library before you're given the next data block.

Over in C# Land we're pretty used to Object Oriented Programming where we can access the entire swath of data, or at least relevant sections of it (like asking for a certain section of vertices to modify for example). Over here in Shader World we can't do that, so the key to working with these two FOR Loops is to create code that can work look at our one data block and depending on what we get, execute different logic.

<br>

### P1-D: It's all 0s and 1s

<br>

The last tidbit I'll leave over here in this section is a note regarding how data is actually stored in Shaders. Sometimes they're stored in arrays, sometimes they're not, but know this: **When it comes to the Frag pass, every relevant data block provided to you by the system that you're going to be manipulating in some way is going to be a float between 0 and 1**. 

And when I say everything, I mean EVERYTHING. Pixels are handed to you in a **Float4** format, which is an array of Size 4 where each entry reads out the RGBA values. Literally `[R, G, B, A]`. If you can start thinking of colors and textures as *just numbers*, some of the funky shader math magic you can do here starts to make some sense. You don't have to internalize this just yet, just keep it in mind: `Colors = 4 floats in a trenchcoat`. We're going to be looking at some concrete examples later.