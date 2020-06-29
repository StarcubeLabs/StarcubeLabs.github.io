---
layout: post
title:  "Unity: Tying Shaders and Particles"
date:   2020-06-30 00:00:00
img: tying-particles/header.png
description: Powering up your particles with code instead of art
author: Spex130
tags: [Unity, Breakdown, Shaders, Particles]
---

## Part 0: The Problem
### What exactly are we trying to solve?


This article details how to make attributes of your particle systems like Particle Lifetime or Particle Color public so that Unity’s HLSL shader system can take advantage of them. Using this technique can help shift tasks like timing for complex shader effects entirely over to the particle system’s built-in methods, which are much more intuitive to interface with. So with that, let’s get to work!


## Part 1: Initialization and Setup
### So what’s a good shader to use with this technique?

Technically, any shader can be modified with particle attributes. Part of the “art” is figuring out how to use these attributes to better your shader. But, for this article, I’ll recommend a basic Dissolve shader since it works well with the “dust cloud” example I mentioned earlier.


<div align="center">![](/assets/img/tying-particles/Smoke_Gif.gif)</div>

<div align="center" style="font-style:italic;" >A basic Dust Dissolve particle shader</div>


The _Dissolve Shader_ has been rather popular as of late. It’s a simple technique that can be expanded into a myriad of different applications. It can be used to create fluffy clouds, intense blazes of flame, or in our case, it can dissolve things like _hits_ or _dust puffs_. I’ve included a shader to allow you to follow along with the changes necessary to implement Particle attributes. 

As a side note, this article does not walk you through the process of how to create a shader from scratch. This shader I have is based on the tips and tricks given by fellow VFX artist [Joyce from MinionsArt](https://twitter.com/minionsart). I would recommend her [Non-Scary Introduction to Shader Code](https://www.patreon.com/posts/19042499) series for those of you hoping to learn exactly how HLSL shader code in Unity works.

Here is the inspector view of a material using this shader I’ve added.


<div align="center">![](/assets/img/tying-particles/image0.png)</div>

<div align="center" style="font-style:italic;">Example Inspector View</div>

The basics of what we have here is that there is a Main Texture which dictates the look of the material, a Dissolve Texture to dictate how the texture dissolves, and a Slider that goes from 0 to 1.2. In this example, I’ve given the same dust cloud texture to both slots. If the Color Value of the alpha pixel on the Dissolve Texture is less than the Value float on our Slider, that pixel location on the Main Texture isn’t drawn. The higher the slider goes, the less pixels exist (and get drawn) that are greater than the Slider Value, and so we get a neat dissolve effect.

Essentially, `if(MainTexture.rgb < DissolveTexture.alpha){DoNotDraw();}`

This is our whole shader we'll be starting with:

```
Shader "Starcube/General Dissolve"
{
    Properties
    {
        _MainTex ("Main Texture", 2D) = "white" {}
        _Dissolve ("Dissolve Texture", 2D) = "white" {}
        _DissolveAmount("Dissolve Amount", Range(0.01, 1.2)) = 0
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" "Queue"="Transparent"}
        LOD 100
        Blend SrcAlpha OneMinusSrcAlpha 
 
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            // make fog work
            #pragma multi_compile_fog
 
            #include "UnityCG.cginc"
 
            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
                float4 color : COLOR;
            };
 
            struct v2f
            {
                float2 uv : TEXCOORD0;
                UNITY_FOG_COORDS(1)
                float4 vertex : SV_POSITION;
                float4 color : COLOR;
            };
 
            sampler2D _MainTex;
            float4 _MainTex_ST;
            sampler2D _Dissolve;
            float4 _Dissolve_ST;
            float _DissolveAmount;
 
            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                o.color = v.color;
                UNITY_TRANSFER_FOG(o,o.vertex);
                return o;
            }
 
            fixed4 frag (v2f i) : SV_Target
            {
                // sample the texture
                fixed4 col = tex2D(_MainTex, i.uv);
                fixed4 dissolveTex = tex2D(_Dissolve, i.uv);
                // apply fog
                UNITY_APPLY_FOG(i.fogCoord, col);
                clip(dissolveTex.a - _DissolveAmount);
                return col;
            }
            ENDCG
        }
    }
}
```
This is the version of the shader we’ll be modifying - a standard Vertex/Fragment dissolve shader based on the common unlit shader generated by Unity. Do note that this version isn’t modified to work with particles yet; we’ll be modifying it as we move further on in the article


## Part 2: Streaming Data to the Shader
### Okay, so we’ve got a shader. What now?

Particle Systems pass data to shaders using something called _Custom Vertex Streams_. These streams take predefined dynamic values directly from the Unity scene and pass them to a shader to be used as live data. In basic terms, it lets you use things like **Particle Location**, **Particle Lifetime**, **Particle Color**, etc. as a variable. 

The first step that we have to do is to create a Particle System that has the options set to feed live data to a shader. To do so, we first need to create a new particle system, and then go down to the _Render_ section. Inside, you’ll find a checkbox to turn on _Custom Vertex Streams_.

<div align="center">(/assets/img/tying-particles/image1.png)</div>
<div align="center" style="font-style:italic;" >Check this Custom Vertex Stream checkbox</div>

<div align="center">(/assets/img/tying-particles/image2.png)</div>
<div align="center" style="font-style:italic;" >The default values found inside</div>

These default values are pasted over the corresponding HLSL properties found inside the shader itself. And these aren’t the only values that can be pasted, by clicking on the Plus symbol at the bottom you can see anything from Particle Location to actual Pseudorandom values:

<div align="center">(/assets/img/tying-particles/image3.png)</div>
<div align="center" style="font-style:italic;" >Examples of other things that can be used as Vertex Stream value</div>

So, you’ll notice that there’s already a property in the default values that matches a property we have defined in our shader:

<div align="center">(/assets/img/tying-particles/image4.png)</div>

What this means is that the _color_ value inside the shader itself is being _overwritten_ by the data being produced by the particle system. Why this is important is because _we can actually control color over the lifetime of a particle_. One key thing to remember about shaders is that at the end of the day, everything that is being passed through the shader is a number and can be used like one. Color values in shaders are passed as a number value from 0 to 1, which coincidentally is more or less the same range of values we are using on the *_DissolveAmount* attribute on our shader. So since we can pass in _Color Over Lifetime_ as a value from 0 to 1, we can replace *_DissolveAmount* with this Color Over Lifetime and get the same effect, but automated by the Particle System. Colors as a whole are passed in via RGBA format - you can use any of them, but I will personally be using the Alpha channel of the input color because the Unity UI separates its slider from the rest of the pack.

<div align="center" style="font-style:italic;" >For this example in particular, we are using Color Over Lifetime - we alternatively could be using just Lifetime, but the first option allows us to control the timing and ease of the effect instead of just getting a rote linear output. Being able to control the ease is a crucial step in VFX feel. </div>

This is how I have my _Color Over Lifetime_ values set:
<div align="center">(/assets/img/tying-particles/image5.png)</div>
<div align="center" style="font-style:italic;" >The colors here don’t change, but I’ve set the alpha to fade over time</div>

So now that we have this data being transmitted to our shader, we need to modify our shader to use the actual output. Let’s take a look at what logic we’ll need to change in our shader itself.

The line in particular we should be looking at is this one here on line 63:
```
clip(dissolveTex.a - _DissolveAmount);
```
This logic uses our Dissolve Amount slider to determine which pixels in our shader not to draw. The HLSL built-in function *Clip* works such that if a value that is given to it is *less than zero*, that pixel is not drawn. So we take our *Dissolve Alpha* and subtract the *Dissolve Amount* from it, and if it ends up below zero, it’s no longer drawn. The larger the *Dissolve Amount* gets, the less we draw, hence the *dissolve* effect. 

What we want is to replace this *Dissolve Amount* (which is controlled via material slider) with *Color Over Lifetime* (which is dictated over time). That way the dissolve over time is handled automatically. 

First we add a line with a new variable to force the Particle Color logic to work the same way as the slider logic(going from 0 to 1 instead of from 1 to 0), then we replace the *_DissolveAmount* with the new variable we’ve set up. We use the “*w*” value specifically so we can access the Alpha value. 

```
fixed4 particleColor = 1- i.color;
clip(dissolveTex.a - particleColor.w);
```
And that’s it! This shader has now swapped over to using the Custom Vertex Stream as its *Dissolve Amount*.

<div align="center" style="font-style:italic;" >But wait, why did we invert the color before we used it?
Well, for the sake of the UX, I have my Alpha slider in Unity going from Not Transparent to Fully Transparent over the life of the particle. Which makes sense, right? At the start I’d like it to be Visible, at the end I’d like it to be Not Visible.
But the actual Clip logic requires that my slider go from 0 to 1, Not Visible to Visible. So we reverse it! Makes sense?
</div>

## Part 3: Configuring the Particle System
### Great, our shader takes in particle data! But how do I test this?

It’s actually pretty easy to set! Simply move down to rendering section down at the bottom of the Particle inspector and place a material using our new shader into the following spot:

![](/assets/img/tying-particles/image6.png)</div>
<div align="center" style="font-style:italic;" >Place your material in the Render section’s Material slot</div>

Now all of your particles will fade over time properly like with real dust puffs. To get the effect we get in the gif at the top of this article, you can use these settings for your particle system: 

![](/assets/img/tying-particles/image7.png)</div>
<div align="center" style="font-style:italic;" >Place your material in the Render section’s Material slot</div>

And that’s it! You can take these same techniques and apply them to any other custom vertex stream that your particle system can output.

Hopefully this helps!

- Spex from Team Starcube
