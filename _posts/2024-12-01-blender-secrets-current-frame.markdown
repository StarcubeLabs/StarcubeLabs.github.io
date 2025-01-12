---
layout: post
title: "Blender Secrets: Using the Frames as input for Materials and Drivers"
date: 2024-12-01 00:00:00
img: current-frame-attributes/frame_att_header.png
description: Powering 2D and 3D animations in Blender using the secret Frame attributes
author: Spex130
tags:
  - Blender
  - Animation
  - Materials
  - Drivers
  - Quickguide
---
# Secret Attributes

I learned a while back from a random forum thread that there are undocumented attributes in Blender that allow you to use the current frame of your Blender animation as an input into your node-based (and presumably driver-based) setups.

I've been using them for the last year, but as it turns out they really ARE undocumented - so I'm writing them down here in a quick note so there's one more place on the internet where it's written down.

## How thorough is this explanation?
...Not very. There's plenty of unknowns since these attributes are kind of undocumented, but they're at least very straightforward in how you use them. Also, this only covers Shader Nodes.

## So, what are the attributes?

There are definitely more, but the ones I use are:
* `frame_current`
* `frame_start`
* `frame_end`

They're pretty self-explanatory.

## How do I use them?

To use them in a Shader Node, create an Attribute Node, set the Type to `View Layer`, then set the name to the attribute of your choice by typing it in manually. Then take the output via Factor, though you could probably get it from any of the outputs. I haven't tested that.

![Attribute Nodes with the three Attributes](/assets/img/current-frame-attributes/nodeview.png)
> Properly set up attribute nodes with all three attributes in view.


## An Example

This setup allows you to, for example, map the beginning and end of an animation to exactly 360 degrees - which further can be used to power rotations of any sort in a perfectly seamless repeatable way.

![Dynamic 360 Degree Remap](/assets/img/current-frame-attributes/nodeexample.png)
> A setup that allows you to get a seamless 360 degree rotation from any arbitrary length animation.


Hope that this is useful!

- Spex130