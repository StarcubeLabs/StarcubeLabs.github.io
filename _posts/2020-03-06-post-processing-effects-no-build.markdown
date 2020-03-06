---
layout: post
title:  "Why aren't my freaking post processing effects in my build?"
date:   2020-03-06 00:00:00
img: house.jpg
description: A 12-part ~~rant~~ post on why the PPS Effect Flow is stupid
---

### The Problem


### The Fix
The essentials to this fix are:
1. That the post processing shader needs to be in the Resources folder since it is loaded using `Shader.Find()`
2. That the tags like `[System.Serializable]` on the `public sealed class` we had inheriting from the parent post processing class should be on the parent class
