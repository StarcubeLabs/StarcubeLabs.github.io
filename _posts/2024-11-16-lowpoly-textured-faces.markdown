---
layout: post
title: "Lowpoly Textured Faces & Blender Part 1: Overlaying Expressions on Models"
date: 2024-11-16 00:00:00
img: facerig/HoodieHeader.png
description: Making facial controls for Atlased 2D expressions in Blender
author: Spex130
tags:
  - Blender
  - Rigging
  - Animation
  - Materials
---
# Lowpoly Textured Faces & Blender Part 1: Overlaying Expressions on Models

## Intro

As someone who primarily works with older-styled modeling techniques dating back to the 90s and early 00s, working with texture atlased eye or mouth spritesheets applied to models is something I do almost every day. I’ve had plenty of trouble figuring out how to get them display properly without instigating z-fighting or simply being too unruly to be manageable. There are multiple ways to do this, and in common game engines like Unity or Godot using shaders (if you’re comfortable with that) can make this easier. But what about if you’re working directly in Blender, where you can’t really “program” logic with code like you can elsewhere?

This is the technique I’ve found that works to allow me to have dynamic 2D atlased expressions that overlay on your model’s basic textures without having to modify those textures. It also allows for those textures to be keyed and animated within Actions, which means you can skip having to work with the Dope Sheet or NLA window.

I am self taught, and so this technique may feel suboptimal or “incorrect” in many ways - but it works quite well for my flow of making models that can be created and rigged quickly, be game ready for Godot or Unity, and still be quickly keyable for rendered out animations in Blender without having to change the rig or any assets much.

This is a guide that involves advanced blender features, but if you have a basic rigged model the steps here are simple to follow.

### What you will need:

- A textured and rigged model that you would like to overlay textured expressions on. Your model doesn't necessarily *need* UV Maps already set, but they'll be easier to work with if you do.

>![Example Model](/assets/img/facerig/hoodieexample.png)
>*My Example Model, Hoodie Girl*

- A texture atlas of expressions to overlay on the model, with a transparent background for each. (Example to come, below)
  

### Skills Involved:

- Skeletal Rigging
- Blender Material Nodes
- UV Mapping Channels
- Custom Attributes
- Drivers
- *A little math*

  
  

## Part 1: Overlaying Textures using Transparency & Extra UV Maps

#### Part 1 - Step 1: Preparing your expression texture atlas

Get a bunch of textures with transparent backgrounds. Arrange them into an atlas of even spaced images. I used [this online tool](https://www.leshylabs.com/apps/sstool/).  
  
Here’s an example (image)

  >![Example Texture Atlas](/assets/img/facerig/HoodieEyes.png)
>*An example of an Eye Expression Atlas*

#### Part 1 - Step 2: Setting up the coordinates for your new textures with a 2nd UV Map

- First, ensure you’ve got the UV Editing Window Tab active, since this view will give us the best work environment for this.  
      
    ![UV Window Tab view](/assets/img/facerig/uvtabview.png)

- Click on your model. Go to the Data Tab in the Inspector and press the “+” button to clone your current UV map. Click on the new UV Map in the list itself to set it to active, then click the Camera icon to set it visible. (These are two different things!)  
	![The Data Tab](/assets/img/facerig/datatab.png)

      
    Double click and rename the UV map if you’d like. Just be careful about renaming it later - Blender uses String Matching to check UV coordinates, not IDs. If you change the name after referencing it, Blender will not auto-update anything that is referencing it via text.  
      
    
- Select the faces where your texture is going to be overlaid.  Make a new UV map that works with how your atlas is set up. If you think it will help for later, consider placing some temporary UV seams around your selection.
  
  Since you are going to have multiple expressions, map your new UV to exactly ONE expression. We’re going to be using Drivers later to dynamically shift this UV map around. 
  
  Don't worry about your old UVs, they still exist in the original UV map! Feel free to be as destructive with this flow as you'd like.
   
    ![Hoodie Eye UVs](/assets/img/facerig/hoodeyeUVs.png)
    >*My new Eye UV maps*

- Next, we’re going to ensure that our overlaid textures will ONLY exist in the places we’ve UV mapped. Invert your selection by pressing `ctrl+I`, and then in the UV window scale the selection down to 0 and place the resulting tiny UV map somewhere on the image that is transparent. Try to put it somewhere where it won’t overlay with any of the expressions so that you don’t get any weird texture artifacts later.  
      
    ![UVs to Scale down](/assets/img/facerig/scaleto0.png)
    >*Scale this down to zero*
    
- The end result should look something like this:  
      
    ![Base UV2 Map](/assets/img/facerig/baseUV2overlay.png)
    >*The Base UV Map 2 look*
      
    Your expression image overlaid on the whole base texture image, and the rest of your model set to black (or alpha, if you have that active)  
      
    
- Click back on your base UV map, and set it to visible. Now, we need to use that UV map to overlay the new texture on the old one!
    

  

#### Part 1 - Step 3: Combining both maps into a single material.

- Swap to the Shading Window Tab for this step. 

    ![Shading Window Tab view](/assets/img/facerig/shadingtabview.png)

> - Remember that for most things in Blender, you can use the Search (Shift-A for the Shading window, specifically) to look for whatever nodes you need. 
    
> - The only ones that won’t be found are Math nodes that are set to specific functions - they rename themselves whenever they are set. I will inform you directly if we use any of these nodes.  
      
    

- Select the object and material that will have the overlaid texture. Find where your main Image Texture node is.
- 
    ![Our base Image node](/assets/img/facerig/shownodes1.png)
    
- Add a new Image Texture, and load in your Expression texture atlas. Then combine the two into a Mix Color node.  
      
    ![Our combined Image nodes](/assets/img/facerig/shownodes2.png)
      
    This…doesn’t look great.
    
- The neat thing is that whenever you have an image with Alpha, that automatically means you have a usable Texture Mask that can be used to help define how things overlay!  
      
    ![Image Alpha Mask](/assets/img/facerig/alphamask.png)
      
    So we’ll use that. 
    
- Plug that into the Factor channel of our Mix RGB node. Where the image is black will show the first image, where the image is white will show the second image.  
      
    ![Image Alpha Mask](/assets/img/facerig/alphaoverlay.png)
      
    That actually made the distinction between the textures clear! But it still looks like a mess, the expression isn’t mapped correctly at all. We need to use our 2nd UV Map.  
      
    

Add a UV Map node, and select your 2nd UV map from the dropdown. Plug it into the Vector of your Image Texture node that contains your Atlas.  
  
![Image Overlay with UV Map 2](/assets/img/facerig/uvmapoverlay.png)
  
Perfect! Now our atlas is overlaying correctly on the model. If all you needed was a game-ready asset, this is now something that can be exported and set up using shaders in a game engine! Next up - Changing expressions, and making them keyable!