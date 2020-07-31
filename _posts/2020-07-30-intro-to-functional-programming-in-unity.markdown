---
layout: post
title:  "Functional Programming in Unity: A Primer"
date:   2020-07-30 00:00:00
img: Unity_Technologies.png
description: Unity Functional Programming Introduction
author: Clock
published: true
---

## Intro

This is a unity (C#) friendly introduction to functional programming.

This article assumes you know the bare basics of c# programming and are familiar with unity.

By the end of this article you should learn the following:
- A no nonsense description of functional programming
- How to use basic functional programming techniques in unity using C#
- What problems lend themselves to functional programming
- What problems **DO NOT** lend themselves to functional programming 

This article does not include real Unity Game Objects in its example, it will only cover high level use cases for c# programming with minimal Unity Interaction. In the next article I will use a real unity project as a basis for the examples.  

## What is Functional Programming

Functional programming really is as simple as it sounds, it is programming using functions. Specifically it is organizing your code to delegate behavior to functions instead of objects (OOP). 

In C# there are a number of ways to delegate behavior using functions. 

## Basic Functional Programming Techniques

### Class Methods

The functional programming abstraction most programmers are familiar with is the class method. A method is a kind of function that is tied to a class.

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MyBehavior : MonoBehaviour
{
    // Start is my only method in this class
    void Start()
    {
        Debug.Log("Oh yeah!");
    }
}
```

We have the `Start()` class method that Unity uses to run startup logic for this behavior class. Right now it just prints a silly debug log to unity. From a functional programming perspective, any users of the start method delegate all startup behavior to this method. 

### Static Method

Right now its just Unity using this method, but consider this utility class. 

```csharp
class MyUtil
{
    public static string GetSpecialDebugValue()
    {
        return "Very Cool and Real Code";
    }
}
```

Now we have another way to delegate behavior, a static method. This class can be called from anywhere, so let's make use of it on our startup method.

```csharp
    void Start()
    {
        string myDebugValue = MyUtil.GetSpecialDebugValue();
        Debug.Log(myDebugValue);
    }
```

This might seem like a silly example, but this concept of behavior delegation to more specialized methods is the heart of all functional programming. 

### Recursion

Let's spice things up a bit with some recursion:

```csharp
class MyUtil
{
    ...
    public static int Fibonacci(int val)
    {
        if (val == 0)
        {
            return 0;
        }
        else if (val == 1)
        {
            return 1;
        }
        else
        {
            return MyUtil.Fibonacci(val - 1);
        }
    }
```

Here we have a method that recursively delegates behavior to itself until it reaches bedrock. Appologies if I gave you job interview PTSD. Recursion is naturally a functional programming technique.

When using functional programming, constantly ask yourself "does this problem lend itself to functional programming or is there another paradigm I could be using?" While this could also be written as a for-loop, Fibonacci naturally lends itself to a recursive functional programming approach, since the mathematical definition of a fibonacci sequence is recursive. We'll cover more situations where functional programming isn't appropriate at the end of this article.

### Lambda Functions

Now we get to the good stuff. We can define a function inline. That is to say we can define a function reference on the fly, then call it at our leisure.

```csharp
    void Start()
    {
        Func<string, string> myFunction = (myArg) =>
        {
            return "Oh Yeah in a Lamba Function, with this argument: " + myArg;
        };
        Debug.Log(myFunction("Some other thing"));
        //Prints
        //"Oh Yeah in a Lamba Function, with this argument: Some other thing"
    }
```

The special sauce here is the variable typing `Func<string, string>` which defines a function
variable that accepts a string argument and returns a string. If we had a second argument that is an integer, the typing would look like this `Func<string, int, string>`. The other bit of syntax to pay attention to is the inline function declaration

```csharp
(myArg) => {
    return "Oh Yeah in a Lamba Function, with this argument: " + myArg;
};
```

The argument type and return value is inferred from the surrounding code. The inline function declaration is treated like a value that is assigned to the variable `myFunction`. Thanks c# compiler!
Here is what the equivalent class method would look like:

```csharp
string MyFunction(string myArg){
    return "Oh Yeah in a Lamba Function, with this argument: " + myArg;
}
```

If you just want to write a Function definition with no return type, opt for the `Action` type instead of the `Func` type.

## Linq

The above techniques are all fun thought exercises but without a framework to use them against this is all academic. Linq is a standard package available to modern .net applications. To use it simply include this line at the top of your file:

`using System.Linq;`

Voila, you have unlocked incredible functional programming power.

As a simple example:
```csharp
    // Start is called before the first frame update
    void Start()
    {
        int[] values = { 5, 1, 2, 3, 3, 1};
        int[] highValues = values.Where((value) => value > 2).ToArray();
        //highValues is an array containing the values '5', '3' and '3'
    }
```

We filtered out a simple array of integers so we have an array with only high values. Linq is not limited to filtering though, here are some examples of common Linq operations, feel free to mix and match:

| Linq Function     | When to Use it |
| ----------- | ----------- |
| .Where()      | Filtering items from a collection       |
| .Select()   | Transforming items in a collection to a different type |
| .Aggregate() | Combine all members of an array into a single value |

### To function or not to function

Oh my, what a powerful programming paradigm. But when do you use this incredible programming ability?

Functional Programming Friendly Problems:
- You have a problem space that can be described mathematically or recursively
- You need to define behavior in your codebase but a class is too heavy
- You need to reason about collections of items

Object Oriented Programming Friendly Problems:
- You need to instantiate objects or use builders
- You need to manage a family of behaviors that are aware of each other
- You need to keep a long running process open that makes heavy use of context
- You need total control of memory allocation in your program

## Wrapping things up

Hopefully I've convinced you give Functional programming a go in unity. You should now have the tools necessary to get started with functional programming. In the next article we will apply these techniques to an actual unity project. Thanks for reading.