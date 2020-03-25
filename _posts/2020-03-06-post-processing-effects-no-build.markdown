---
layout: post
title:  "Why aren't my scene's PPS effects in my build?" and other "Silly Questions"
date:   2020-03-06 00:00:00
img: other-silly-questions/header2.png
description: What to do when the Post Processing Stack isn't cooperating.
author: Spex130
tags: [Unity, Breakdown, Post Procesing Stack]
---

### The Problem

Occasionally in Unity 2019, the Unity Post Processing Stack (PPS) will entirely fail to save in your scene. This can be fairly frustrating - everything else in the scene seems to save fine, but something about the PPS component attached to the camera just won't "stick." All of the options set on your scene camera will fail, and on top of that saving and building the scene after resetting the values will still output a build with the PPS effects missing. It's like you have a trial version of the Post Processing Stack, and trying to do anything productive with it is just meant to fail. So, why is this?

### The Technical Behind-the-Scenes

The reason for this might possibly be improper serialization. Recently in our most latest Global Game Jam game [Astromender](https://globalgamejam.org/2020/games/astromancer-8) this issue cropped up around the second day and gave us some real chaos in the face of the looming 2-day time limit. We had coded up a Game Boy inspired visual effect that added a neat 8-bit flair to our game, but upon finally building the project to test everything the PPS effect we had specifically coded was missing from the build. We went through the standard PPS debugging methods: We had it referenced in the "Always Included Shaders" list under "Graphics" section of the Project Settings, and additionally placed the offending shader into the "Resources" folder to ensure it could be found by unity. However, no matter what we did the shader simply would not show up in the final build. 

After tracing back our commits and having the team debug the suspected files, we eventually found the culprit: the offending code ended up being our serialization tags applied to the top of the Game Boy Pixel effect.

This bit right here (paraphrased):
```
System.Serializable
[PostProcess(typeof(OilPaintPostProcessingRenderer), PostProcessEvent.AfterStack "Oil Effect/Oil Paint")]
public class...
```

As it turns out, we had a hierarchy of sorts - we had done two PPS effects separately, and then had one inherit the other to combine both effects. Unfortunately, the serialization tag at the top (`[System.Serializable]`) was in the child file, not the parent file. So while it would work in our local scene, it wouldn't properly serialize in a way that would allow it to be included in a build. It would silently fail due to the hierarchy issues we had directly coded in. We ended up fixing this by moving the serialization tags to the parent class - which allowed the rest of our debugging steps (the "Graphics" section and the "Resources" folder) to properly apply.

### The Fix
The essentials to this fix are:
1. That the post processing shader needs to be in the Resources folder since it is loaded using `Shader.Find()`. This methood WILL NOT find the shader unless it can be found inside the Resources folder.
2. If you are including any inheritance, tags like the `[System.Serializable]` we had on our `public sealed class` parent post processing class should be on the parent class or the file will not be included in the build process.

Hopefully this helps anyone else who happens across this issue. It certainly gave us a lot of trouble when we found it!

- Spex & Mitjmcc from Team Starcube
