
---
layout: post
title: "Starting a new project - Simplifood"
date: 2025-03-12
categories: [Simplifood, Web Development]
tags: [side-project, learning, react, rails, tailwind, accessibility, neurodiversity, cooking, recipes, pwa, ai-assisted]
---

## Intro

I'm currently (f)unemployed, and in between living my best life of reading, playing boardgames, watching TV and hanging out with my dog and partner, I decided to embark on one of those "side projects" I've heard so much about.
## A side project you say?

I wanted to build something to keep my web development skills honed, and after spending 5 years at my previous job, I feel slightly institutionalised and am wanting to try some new (at least to me!) tools.

To keep myself accountable, and track my progress, I have also decided to blog about it. You found my blog - welcome!
## The problem I want to solve

I was recently diagnosed as autistic, and one of my special interests is cookbooks, and, to a lesser extent cooking - I'll admit that I tend to read about food more than I cook! Despite loving cooking, I often find myself too tired to cook, or I haven't left myself enough time - because I didn't realise I was getting hungry, and I now really desperately need food within the next 10 mins.

However for me, trying to find inspiration or a particular recipe online, is very overwhelming.

---
## A quick detour down the Recipe Rabbit hole

**In a nutshell**: Finding an online recipe is overwhelming and stressful, and takes a lot mental energy.

**In a larger, stream of consciousness, more coconut-sized shell that you should feel free to skip**:
Trying to google anything recipe related leads to a bombardment of websites. When you think you might have found a recipe that sounds promising, you face flashing adverts, and videos everywhere. You start to read, skimming the text for useful information about whether you want to commit to this recipe, and find yourself reading about the author's recent trip to the Amalfi Coast, which is irrelevant to this recipe except that it uses a lemon. You start to scroll faster, looking for an ingredient list in amongst the adverts. You find it. The page does some kind of refresh and resets your place. You scroll again. You find it! Everything is in cups and farenheit and unfamiliar sounding ingredients. It's American. Do you stay here, as it does look good, or try to find an equivalent UK one? You google the recipe name and add "UK". You skim a couple of recipes. They don't look as good as the American one. You go back to the American one and start to google the conversions from cups to grams or ml. You have most of what you need, and don't think the missing bits are too important. You've wasted so much time trying to find an appropriate recipe that you know you're really up against the clock now. Your concentration is failing, you have a headache, and you feel weak. You start to cook it, and continually have to re-look up the ingredient conversions because you didn't write anything down, and have to remember to skip the bits that you're not doing. Finally, dinner is ready. That's if you stuck with it and didn't abandon at some point to just get fish fingers out of the freezer or order a takeaway.

----

The above may sound overly dramatic, but that is my reality, on multiple evenings of the week.  If I can get on top of things, and organised - to plan and shop for a particular recipe, or know what I'm doing ahead of time, it's much easier. However, if I don't have a plan, and I don't start cooking early enough, it can be a nightmare.

And this is for someone who actually enjoys cooking, and finds it fairly straightforward to follow and adapt a recipe. I know that there are a lot of people out there, especially neurodivergent ones, for whom this is an almost insurmountable task - especially when you throw in executive function issues, and sensory difficulties with certain foods, as well as the light, heat and sounds of a kitchen. There are also a lot of people with dietary restrictions who struggle to find recipes that work for them. As well as people (I used to be one of them) who believe that a recipe is a rigid set of rules that must not be deviated from - rather than some suggested guidelines that you can and should adapt to work for you.

When I did some research, I found that there was surprisingly little online to cater to the neurodivergent community in this area. What there was, mostly seemed to be geared towards parents teaching their neurodivergent teenagers how to cook, and even those seemed to just be a recipe to follow, with very little to allow for removing or substituting ingredients.
## My solution: Simplifood - "Food, simplified"
 
I plan to build a Progressive Web App (a website that has some app-like features) focused on simplicity and flexibility.

**Core features**:
- A small set of "Rescue Recipes" for quick meals ready in under 15 mins using common store cupboard / freezer ingredients
- A small curated set of foundation recipes with clear step by step instructions
- Options to remove ingredients, which will dynamically update the recipes
- Step by step viewing options to reduce mental load (1 step, 3 steps or full recipe)

## Technical Approach

I want to work with a generative AI to aid with infrastructure and coding - I'm interested to find out the capabilities and limitations of working with AI for a full project.

**Backend - Ruby on Rails**
This is what I am most familiar with, and can work fastest with. I think an Object Oriented approach makes sense for the flexibility required. There will be multiple relationships between entities (e.g. recipes, ingredients, steps) where changing one, will affect other entities and I think a relational structure makes sense.

**Frontend - React with Tailwind CSS**
These are both new to me (after a brief foray into React 10 years ago), but I'm excited to try them.
I think React is a good choice for a highly interactive UI due to its state management to help track recipe modifications, and the Virtual DOM to efficiently update without a full page reload.
I have only really used Bootstrap in the past, and wanted to explore a more modern CSS framework like Tailwind CSS which I understand is growing in popularity. I'm also interested in trying utility-first approach, as I haven't used this before.

**Ops**
Choices here are mostly based on low cost, as well as ease of use. (And may change once I start developing in earnest):

**Hosting**: Fly.io - it seems like a more affordable alternative to Heroku at this early stage
**Database**: Neon - a free tier for Postgres db
**Bug tracking**: Sentry - a free tier for solo developers
**CI**: Github actions - I've been wanting to explore this, and seems like a cost effective solution
## Learning Goals

I'm excited to build a project completely from scratch and round out my full stack skills. I tend to gravitate more towards the backend work so wanted to challenge myself to get a better understanding of frontend work, and solidify my CSS skills.

- Develop a good working understanding of React
- Practise and strengthen my CSS knowledge
- Deepen my existing knowledge of Product work
- Give me greater confidence in my overall full stack knowledge
- Learn more about how generative AI can enhance my work

## Challenges

I think one of the biggest challenges will be working with technologies that I have no experience with, without any colleagues to support me - I'm hoping that working alongside AI will help with that. I also have a lot of ideas for enhanced features, and I think it will be easy to get carried away and try to build too much, and get discouraged. So realistic expectations, and drastic prioritisation will be key!

## Next Steps

Getting some content to experiment with will be crucial, so I think next steps will be: 
- Deciding on the architecture
- Getting a barebones version of the admin CMS running
- Writing up a few recipes to test the main functionality with
- Designing some wireframes

I am deliberately trying not to set any time limitations on this, so haven't thought too much further ahead. However, I plan to write a blogpost very week or so - even if it's to say that there has been little to no progress! If you are interested in following along, then please sign up to the RSS feed.
## Get involved

I want to help people who feel overwhelmed by cooking, to learn to cook some simple, tasty recipes. Cooking from scratch is healthier, and often cheaper, and it seems extremely unfair that this should be essentially closed off to a huge number of people because the existing resources are unusable to so many.

Even if nobody else apart from me ever uses this app, I'm excited to finally have one place where I can store my favourite recipes to cook from, for when I'm feeling hungry and overwhelmed.

If you think that Simplifood might be something you would find helpful, please take some time to fill out this questionnaire (it should take you no more than 5-10 mins): https://forms.gle/hngtMdtpWGg1sXZZ8

