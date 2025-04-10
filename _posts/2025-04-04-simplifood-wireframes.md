---
layout: post
title: "Simplifood: Wireframes and React Components"
date: 2025-04-04
categories: [Web Development, Simplifood]
tags: [side-project, react, figma, wireframes, design, ui, tailwind, ai-assisted]
---

Having got the skeleton structure of my app set up, I wanted to start focusing more on the details - how it will look, and the type of information that I want to include.

## Wireframes

I've got stuck into wireframing with Figma, which is a joy to use! The last time I needed to create my own wireframes, was around 10 years ago in web dev Bootcamp. At the time I used Balsamiq which I loved, but I don't think it has a free tier any more, just a free trial. I've done other bits and pieces with Excalidraw, which is great, but thought I'd try Figma to familiarise myself with it. It offers 3 free pages, which should be enough for my purposes.

It's really nice to use, with all sorts of helpful snapping to corners and showing you where the middle of everything is - delightful! I've started off with a mobile design, as that's what the majority of people like to use in the kitchen, according to my small pool of [survey](https://forms.gle/SUgSfMPcDnG2LqMMA) responders. Design is really not my strong point, so I've decided to stick to a really simple palette as I think I'm less likely to make egregious design mistakes that way, and I want the whole look to be quite stripped back and simple to avoid visual overwhelm.

Here is my current attempt at the list view (first draft - will likely add more data): 

![Simplifood Recipe List wireframe](assets/images/list_view_wireframe.png)

I'm planning on doing colour photographs of the dishes themselves, but otherwise everything will be monochromatic.

There will be a variety of different information on the recipe cards in this list, but I haven't finalised everything yet. One thing I do know, is that I want the language to be as inclusive as possible, and avoid making assumptions. I've decided to go for symbols to represent "effort" level, rather than "difficulty". I may change the symbols but am going with wavy lines for now:

- 1 wavy line: minimal effort - few steps, straightforward process
- 2 wavy lines: moderate effort - multiple steps, some attention required
- 3 wavy lines: significant effort - complex process, sustained attention needed

This kind of categorisation is always highly subjective, but I think there are many different ways to label a recipe and I'm trying to come up with the information that's most important. Stir fry or scrambled eggs are simple recipes, that would often be labelled as "easy". But they do actually take a certain amount of effort to make - you need to pay attention, and do some regular stirring, requiring more attention than something you can pop in the oven. Even though the oven dish may require a bit more preparation to get ready. So I want to think quite carefully about how I categorise the recipes, and hopefully give enough helpful info that people understand what's involved at a glance.

## React
I've made my first tentative foray into the world of React, and it's all making sense so far! Claude.AI was keen to just write the final version of all my main components, complete with styling, but I decided to take a step back and take it slowly.

It's not much to look at, but I was pretty proud to get this all rendering correctly and in the places I expected.

![Simplifood basic React components](assets/images/basic_react_components.png)

I've also got a recipe service built to consume the data from the API, but I'll need to do a bit more reading around that to make sure I've understood it.

## Fake data
Claude really came into its own with the creation of my seed data! Was able to generate some fake recipes and ingredients (that sound plausible I might add!) extremely fast. However, it missed a really key join table - StepIngredients which is crucial to being able to remove an ingredient and the recipe updates. Luckily I read through the fake data carefully and then it could be regenerated super fast again. This is the kind of stuff that I'm really enjoying getting Claude to do for me - boring, repetitive linking of things that would take ages. It's able to produce detailed fake data extremely quickly.

## Next steps
- Updating the React components to display the recipe data coming through
- Getting to grips with Tailwind CSS and adding styling