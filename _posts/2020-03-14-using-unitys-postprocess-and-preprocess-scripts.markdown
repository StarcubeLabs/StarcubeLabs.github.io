---
layout: post
title:  "How to use Unity's Preprocess/Postprocess scripts"
date:   2020-03-14 00:00:00
img: logos/StarCubeHeaderWide.png
description: Are you tired of making the same changes to imported files in Unity? Here's how to automate those workflows.
author: Clock
tags: [HowTo]
---


# What is Preprocessing / Postprocessing?

Preprocessing is exactly what it sounds like. You have a *Process* that runs before ordinary processes. Post process is the opposite.

## Why is this a useful feature of Unity?

With Unity Preprocess and Postprocess Scripts you can:

* Automate tasks you are manually performing on imported assets

  * Unity will try to import assets so the imported assets are easy to manage. It may be preferable to add your own spin on unity's import processes. You'd do this because you either have a project specific way you'd like to handle your asset's data or you don't like how unity imported your assets. I'll cover an example of this behavior later in the article.

* Create a custom workflow from imported asset meta data

  * Assuming your file type supports metadata and unity has captured the information on import, you can modify your imported object however you'd like with that information.

* Create in-editor callbacks to automatically update the scene in your project.

  * This is a more niche use of Unity's asset importer. Assuming you have other editor scripts in your scene waiting for a signal from the asset importer, you can fire a custom event to those scripts. This is an involved process involving static event handlers, I'll cover how to accomplish this behavior in a later article.


## Renaming imported fbx animations in a unity postprocess

Ordinarily Unity will import .fbx animations with the following naming scheme: "<model>|<animationName>". For simple projects, this naming scheme is fine. However if you'd like to reference animations by name, or you work intimately with the unity animator this naming scheme quickly becomes tiresome. 

For the game I am currently working on, I use the following Unity AssetPostprocessor editor class to rename animation clips. By placing the below class in the 'editors' directory as a child of my project's Unity assets directory, I was able to force imported animations to remove the "<model>|" prefix from animations. Now all of the animations imported maintain their original names.

```csharp
public class AssetImportPostProcess : AssetPostprocessor
{
    
    private void OnPostprocessAnimation(GameObject root, AnimationClip clip)
    {
        if (clip.name.Contains("|"))
        {
            clip.name = clip.name.Split('|')[1];
        }
    }
}
```

## Further Reading

https://docs.unity3d.com/ScriptReference/AssetPostprocessor.html

## Wrapping Up

I hope this helps your project become more manageable. Thanks for reading.

~ Clock from Team Starcube
