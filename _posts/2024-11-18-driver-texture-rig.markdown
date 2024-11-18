---
layout: post
title: "Lowpoly Textured Faces & Blender Part 3: Texture Animation Rigging using Drivers & Bones"
date: 2024-11-18 00:00:00
img: facerig/hoodieheader3png
description: Animating Textured Expressions using Bones, Custom Properties, & Drivers
author: Spex130
tags:
  - Blender
  - Rigging
  - Animation
  - Materials
---
#### Part 3 - Section 1: About Custom Properties.

Okay, so it’s neat that we can shift expressions now, but how do we make this practical for animation?

By using Custom Properties! Blender allows you to add Custom Properties to anything, whether they are Strings, Floats, Ints, or other data types. The section to do so can be found at the bottom of every single section of every tab in the inspector.


![Where you can find the Custom Properties](/assets/img/facerig/custompropertiesview.png)
> *You can find this in literally every tab, you can add Custom Properties to ANYTHING.*

  
In this particular example, we are going to be adding a Custom Property to a bone held within a rigged model’s skeleton. The reason for this is actually related to animations and how they work! 

Animation views like the Non-Linear Animation window or the Dope Sheet are mainly concerned with the flow of animations as a whole from a more global perspective. If you want to contain multiple animations within a single file in Blender, you need to use Actions and the Action Editor. However, Actions are only capable of interacting with the keyframes of Objects that can be interacted with in the 3D View. If you’re keying a Skeleton in Pose mode, that works! If you’re transforming object locations, you can see those keyframes as well! But if you try to keyframe properties like Material definitions or other variables within views like the Material Inspector, those do not show up as keyframes within the Action editor. This effectively bars them from being reasonably included in multiple Action animations, as they play the same keys globally no matter what Action is currently playing.


This changes however, for Custom Properties added to bones within skeletons. The Action Editor is very concerned with the properties associated with bones, so if you key a Custom Property attached to a bone, it will show up as a keyframe in the Action Editor!

So, how do we do this?

#### Section 3 - Part 2: Setting up a Controller Bone

We’ll need to make a bone that is specially designed to not interact with objects rigged to its skeleton, and then place it out of the way of the other bones in the skeleton. I’ll also be changing the visual of my Controller Bone to make it more unique and easy to work with.

So, first, enter your skeleton in Edit mode, make a bone and attach it to the origin bone of your hierarchy. Make sure it isn’t Connected to the origin, we’ll want to be moving it elsewhere. Go ahead and place it somewhere out of the way from the rest of your skeleton - I put mine above my character’s head. Name it something distinctive. I named mine PropertyBone.

Go into its options and uncheck the Deform checkbox to ensure that rigged objects will not consider it when setting up bone weights. Find it in the Bones tab in the right inspector, under "Relations" and "Deform".

![Make the bone, disconnect it, make sure it doesn't deform.](/assets/img/facerig/disconnectdeform.png)

Now, switch to Pose mode

Press ‘N’ in the 3D view to bring up the Right Sidebar (or use the Layout view). Look at the Item sidebar tab and click the Lock next to each transform for our new bone. This will prevent it from being transformed by anything besides its parent.

![Lock all the transforms for your bone.](/assets/img/facerig/transformlock.png)

As a bonus, I used [Bone Widget][bone-widget] to make a custom shape for this bone, but if you don’t have that plugin you can use this option in the Bone tab of the side Inspector to add your own custom shape for the bone. 

![Custom shapes can be assigned here](/assets/img/facerig/customshape.png)
> *Custom bone shapes can be neat, but also can be complex. We won't be going over that in this tutorial, but use them if you can! They really clean up the visuals of complex rigs.*

With that same bone still selected in Pose Mode, scroll down to the Custom Properties dropdown in the Bone inspector tab and click on New. Select the gear next to your new property to edit it.

![Adding a new Property](/assets/img/facerig/newprop.png)
  

Set its type to Integer, and give it a distinctive name. Set its Default Value to 0, and leave its Min as 0 as well.  
  
It’s Max should be the max number of cells we have in the Sprite Atlas. In my example, that’s 3 * 2 - 6!

Well, almost. We coders start counting from Zero, so instead of going from 1-6, we go from 0-5. Set the Max to 5 and press Okay.

![New Property Settings](/assets/img/facerig/propsettings.png) 

#### Section 3 - Part 3: Driving with Drivers, Keying with Keyframes

Right click on the slider of your new property and select Copy as New Driver. This will let us clone whatever this value is to other values within Blender.

  ![Copy as New Driver](/assets/img/facerig/drivercopy.png)

Swap over to the Shading View Tab, and select your Object and Material that contains our Current Index value node. Right click on the slider there, and Paste Driver. 

![Paste Driver](/assets/img/facerig/pastedriver.png)

It should turn purple, like so.

![Successful purple driver in node](/assets/img/facerig/purpledriver.png)


Now, whenever we change the Custom Property we attached to the new PropertyBone, this value will change in the same way! And since we set it to be an Int, the jumps between expressions will be instant and exact instead of a scrolling animation. The custom properties will even show up in the Item tab of the 3D View Sidebar (accessible by pressing 'N' within the view) if you have the item with Custom Properties selected! In this case, that's our PropertyBone! Try it out!

![Side Panel Property Control](/assets/img/facerig/sidepanelprops.gif)
> *This is a prior model I set up with separate expressions for eyes and mouth, but it still applies!*

And what about animating it?

  
Let’s look at an animation in the Action Editor, which can be found as this dropdown in the Animation View Tab.

  

You can right click on the Custom Property and Insert Keyframe to key that value on the current frame. And if you have the Record / Auto-Key button active, whenever you change the Custom Property a key for it will appear as well. The keyframes will show up as frames for your new bone - except since we locked every keyable thing for that bone generally speaking the only properties that will end up keyed will be your Custom Property. And since they’re keyframes like anything else, you can shift them where you want on the timeline as you please with no issue.

![Side Panel Property Control](/assets/img/facerig/keypropanim.gif)
  

And with that, you should now have a completed fully animatable expression rig that utilizes keyframes and actions!

[bone-widget]:https://github.com/waylow/boneWidget