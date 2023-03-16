---
layout: post
title:  "Att's Assault: An 7DRL challenge retrospective"
date:   2023-01-25 00:00:00
img: 7DRL/attsAssault.png
description: A retrospective 
author: Clock
tags: [Retrospective]
---

# Intro

This is a retrospective/post-mortem on development for our game Att's Assault for the [7DRL 2023 roguelike game jam.](https://itch.io/jam/7drl-challenge-2023). We (several members of Starcube) worked for 7 days (sometimes more) to create a roguelike, Att's Assault. We're all extremely pleased with the result and I felt it would be a waste to leave out any important lessons learned. I'm writing this post to collect my thoughts and share with anyone interested about our dev processes.

***Important Note:*** Unless otherwise mentioned, these are my personal take aways from the jam.
<br> 
<br> 
## What worked

### Pre-jam Setup

According to the rules of the 7DRL game jam, our team was permitted to bring in existing code. This was a solid opportunity to get our game off to a solid beginning. I took the extra days to setup a basic input system, a scene management flow, and the framework for a roguelike's procedurally generated layouts. The first two would be necessary in any game, and the last one was a must-have feature for roguelikes. Since these items were setup by the time the jam started work, we could instead focus working on what made our game unique.
<br> 
<br> 
### Test Levels/ Feature Sandboxing

Combining game dev work from multiple team members during development is a notoriously difficult process. Doubly so during a high intensity development sprint like a game jam. Without a special process devs can run into code conflicts that have to be resolved.

To solve this the team pointed out a concept to me to resolve this; "Test Levels" or "Feature Sandboxing". The idea is you stage game features, mechanics, models, etc. onto a unique scene. This way the conflicts are mostly limited to shared code and the main game scene can remain pristine until we start a final pass on the game.
<br> 
<br> 
### Game Vision

Att's Assault was pitched to us by Spex as "Pokemon Mystery Dungeon", a pokemon themed roguelike. Most of us had at least a loose familarity with the game, so it was easy for each team member to make reasonable assumptions about how the look/feel of the game. Spex also added "let's focus on making a quality roguelike first", this served as a solid direction to take most early dev work. 
<br> 
<br> 
### Motivation and follow through

When team members set aside time to work on the game, there was no need for external motivation; everyone was extremely excited to be present and working towards the game vision. Same thing for following through on the promise of a finished game. All members of the team were extremely professional and wanted nothing more than to work on the project to the best of their abilities.
<br> 
<br> 
## What could be improved
***Note***: I am just as guilty as doing these things as anybody else on the team.
### Learning during the jam 

There were a number of times during our jam where team members were blocked on their work until they got direct help from other teammates. In ordinary dev time, this would be a fine learning experience. In a game jam sitatuation, asking another developer to drop what they are doing to assist in a learning task is lethal to productivity; it is a context switch for the helping developer. Not knowing how to do something isn't a moral failure. However, it is crucial skill to weigh how much help you need to do a task before you accept said task.

Game jams can be a great of source of learning. "Necessity is the mother of invention" they say! Though this learning is not usually what people set out to do when they start a game jam. 

Jams are where the metal meets the road. Your team should be mostly executing by the time work gets underway. Otherwise a finished product is in jeopardy and that would defeat the purpose of a jam. Less "figure it out as we get there", and more individual preparation/training beforehand. Obviously this can't be helped sometimes. Even the most senior developers do not know how to do everything.
<br> 
<br> 
###  Team members should implement their own work into the game

There were a few times in the jam where members of the jam were integrating other members work. Whenever the contribution didn't exactly line up with how the game worked or there needed to be slight adjustments, things needed to get kicked back to the original author. A few back and forths plus wasted time context switching and you have a productivity killer on your hands.

There's a tedency for team members to get "siloed" off from the rest of the game and do their own thing. "I'm a coder, I just do code things" or "I'm an artist, I just need to do some art for the landing page".

All the work for a game must be put into the game by someone and it is far better for the original creator to integrate their work into the game than another team member. That way the original contributor can see their work as part of the total experience instead of throwing work over the fence. If there's adjustments that need to get made to the original work, who's a better team member to make those adjustments: the original creator or an integrator?

By no means am I saying all team members must be a jack of all trades. Merely that the closer a team member can get to integrating/adjusting their own contributions in the context of the game, the more consistent the end product will be and the less friction there will be for other team members.
<br> 
<br> 

### Identify pain points as early as possible and fix them immediately

Each level of of Att's Assault contained a list of all items and enemies with an associated weight. Anytime a new thing needed to be added to a floor, a new entry needed to be added to the floor's list, and an integer weight needed to be chosen for the entity representing how often that thing would appear. The unity scene selector was also broken so this meant each entity must be dragged into the floor's list from a prefab file in the project (As opposed to a searchable list of compatible objects). The list also did not account for duplicates entries, did not give any insight into the effective frequency of when an item could appear on a floor and could not be composed with other floor data to reduce hand jamming the same values. We also did not have support for guarranteeing that an item could appear on a level, the work around was to have only one item in the table for particular floors.

This is one example of a workflow that worked fine for the jam but could be improved in future jams. Perhaps an improved inspector gui, a purchased unity asset or a different code organization could have made the process of tuning levels a little easier.

Game development is an exercise in iteration. The more loops you can make from design, to implementation, to testing, the more polished of an end product you will have. If there are any hiccups in that loop that need to get smoothed out, destroy them. Become a T-1000 of annihilating things that get in the way of iterating on your game. For example

- If an art process isn't working, retool that process or throw it out and start simpler
- If developers need the codebase to be worked on in a special way and stay that way, they should inform other developers
- If a visual tool is not behaving properly and slowing down your design process, fix it to make sense for your needs before making more design changes 
<br> 
<br> 

### Get the game working first, iterate/expand on it later 

In the [Pre-jam Setup](#pre-jam-setup) section, I mentioned that selecting work items that I knew we needed was a great idea. On the flip side, we did run into some feature creep during the jam. We knew that we needed roguelike elements for the jam, but we weren't sure which elements of roguelikes we wanted to implement for our game. After implementing turn based combat we started churning on game mechanics. Some of the features would not be completely fleshed out as finished gameplay mechanics until a later in the jam. Some features are still uncommunicated to the player in the end product. 

It is difficult to make a judgement call in the middle of development of when to choose focusing on moving towards the end product vs iterating/expanding out features,mechanics and content for variety. I think ultimately this a case by case basis depending on the feature and the decision of the team. "What is absolutely necessary to the finished product" vs "wouldn't this be cool?" Make no mistake though, it is a decision that needs to be made and it does require getting everyone on the same page. 

Games are not a collection of mechanics and features; they are a single essential experience created by smaller impressions. To get the clearest picture of how your game works at any point in time, it is important that the game be as close to "deliverable" as possible. Meaning if development suddenly stopped at any point, the team could turn in the current scene and it would have the last slice of working features in it.
<br> 
<br> 

## Things that I would have done differently

### Meeting zero

I think it's safe to say that even though most everyone on the team had a singular idea of a finished project at some point during the jam, we all started with wildly different ideas about the intensity and quantity of work we would be contributing the jam. There were also some developers that were stretched more than others because we did not have a clear division of labor and/or area of responsiblity. I think an initial meeting for all the developers would have cleared up a bunch of that confusion and identified skill gaps earlier.

### Standarization and style

I took it upon myself to do a lot of the setup for this game jam because I thought I'd be the one doing the lion's share of the programming. I found out later that there were additional programmers that joined me in the codebase so they had to live with the early coding decisions I made for the rest of the jam. I think I did a decent job of setting up the architecture, but it was inconsiderate of me to not include any other developers in that decision making process. It would have also taken a lot of the guess work off of the developers plate's if I had communicated my decisions out at the start of the jam.

Without knowing much about the art creation process, I'd imagine the artists feel the same way.
<br> 
<br> 

## Things of note

### "Requirements"

Consider the difference between two development styles: waterfall vs agile. Broadly speaking waterfall encourages a linear list of todo's that eventually become a finished product. Agile encourages iterating on a deliverable product at every stage.


![Waterfall vs agile](/assets/img/7DRL/waterfallVsAgile.bmp)

Waterfall expects all requirements to be defined up front before any development can be done. Agile merely starts with a product vision and constantly iterates towards a finished product. Static upfront requirements asking for things vs. Iteration on a living piece of software. The difference is stark, no? 

The idea of having all decisions set in stone before any work done is stifling to how quickly things change in a jam environment.
There are no "requirements" in a game jam. The closest thing to "requirements" in a game jam is a current product vision and the individuals best attempt at moving the game in that direction. If one developer identifies a work item for another developer, the receiving developer can expect a conversation, a communication, or a direct message around what needs to get done. The developer being asked to do things shouldn't expect a bundle of specifications about how things must be done.  

<br>
<br>

### Stone soup

What is stone soup? It's a tale from the excellent book "The Pragmatic Programmer". The story basically goes a group of travelers makes camp for the night in a famine stricken village. They had't eaten all day long so they began knocking on everyone's doors begging for any food they might have. The villagers were suspicious of the strangers in their town and gave them no food. Undeterred they set up a cooking pot to make a stew. Instead of food they began casting stones into the boiling water. The townsfolk could not contain their curiousity anymore and approached the travelers to ask them what they are doing. 

"We're making stone soup" - one traveler answered
"Stone soup? What's that?" - asked a villager
"It's a delicious soup made from water in stones. It would be much better with some carrots though" - another traveler answered
"I have some carrots!" - said another villager
"That's great. And potatoes would really help too" - added another traveler
"I've got potatoes and some pork too!" - intoned another villager 

Eventually the soup was finished and everyone in the village ate despite the famine. 

There are many morals to this story but the one most salient to the jam is this: as someone collaborating with other individuals, it is far easier to work with proactive people that add things to the stone soup (project) than it is to work with people that wait for instructions.

Inevitably during the jam, there were moments where developers had to stop and ask out loud to the rest of the team "hey what am I doing? What am I supposed to be doing? What are the exact requirements of that? Can you help me do that?"

Be self motivated, make best guesses towards what contributions add towards the finished project, and be patient when your contributions need extra rework because the first pass did not fit into the bigger picture. Every team member is ideally working towards the final product in good faith and you have to trust them as much as you expect to be trusted.

## Final Thoughts

This YouTube video is our team I think. (Click it)

[![2001: A Space Odyssey Orchestra](http://img.youtube.com/vi/zPW9wadnJpo/0.jpg)](https://youtu.be/zPW9wadnJpo "2001: A Space Odyssey Orchestra")

What you may not immediately see in this orchestra is they have swapped instruments. It's besides the point that they sound terrible. At any point in time during the actual concert, one of these musicians could blink out of existence and his peer could take his place instantly and play very close to what sounds like a 2001 a space odyssey. Funny though that video may be, those musicians are insanely talented. Our team exhibited this skill set during the jam; at any point in time if someone needed to leave another team member could pick up about where they left off without skipping a beat. You just do not see that level of skill and flexibility just anywhere. Only in Starcube ⭐
