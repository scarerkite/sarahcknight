---
layout: post
title: "Simplifood: Basic app setup with AI support"
date: 2025-03-26
categories: [Web Development, Simplifood]
tags: [side-project, rails, react, tailwind, ai-assisted, setup, database-design, learning]
---

I took most of last week off, except to set up a project Trello board and think about my app's architecture. This week I have knuckled down and did two solid days' work getting things set up. I am using Claude AI to assist, and have to say that I am shocked with the amount of progress I have made in those 2 days.

Here is the basic architecture so far:

![Simplifood Database Diagram](/assets/images/simplifood_diagram3.png)

I have gone with join tables, as I think flexibility is the key. It's nice that although I think some of the code may get complex with trying to provide as much choice as I'm imagining, the base requirements are actually quite straight forward: Recipes, Ingredients, Steps, Equipment

I don't see that there will be a need to expand much beyond this, except adding users and then maybe some more models related to them, and preferences etc. I think the key will be in the categorisation of the recipes, to allow users to find exactly what they want.
## AI: mixed feelings

I confess I have very mixed feelings about using generative AI. In some parts of the internet it's seen as the latest essential tool that you are essentially a luddite and a fool not to take advantage of. In others it's destroyer of the environment, stealer of words and livelihoods, and potential destroyer of humankind or at least civilisation as we know it.  I think all these things can be true at the same time, but they could also be true of various other things that we do every day, and you know, capitalism etc. For the moment I feel like it's something I need to embrace if I have any future in my career as a web developer, so I am setting aside some of my worries but also feeling a bit guilty. Maybe the time will come when that's not a compromise I'm willing to make any more. For now, I'm somewhat reluctantly jumping on the bandwagon, and then sticking my head out of the bandwagon window, feeling the wind in my hair and marvelling at the speed and magic.
## Setting things up

I have to say that when it comes to all the boring stuff of setting up a new project, Claude excels. This is something I have to do so rarely, that I always feel like a beginner when it comes to getting a project set up from scratch. I inevitably choose the wrong Github settings when cloning a repo, and there are often so many choices and ways of doing things that I quickly feel overwhelmed by all of the documentation I need to read through to try to make a decision on something I haven't used before. 

Asking Claude questions, and getting it to set up all the boilerplate stuff has been a dream.
I've mostly deferred to Claude's advice on how things should be set up, as the main area I want to practise is coding the app and getting more experience working on the Frontend. Once I've started doing that, I may find that I've made some horrible choices but I think I need to get stuck in first and I'm not overly fussy on how I get there.

Here is a rough list of things that I've achieved so far, based on the Trello cards I have written up:
#### Basic app set up with core models

- Create a new Rails API application
- Configure Git repo
- Design DB schema - recipes, ingredients, steps
- Create main models with relevant attributes and associations
- Set up validations and relationships
- Write up some specs
#### API foundation

- Implement API versioning
- Create basic recipes controller with index action
- Add show action for individual recipes
- Set up JSON serialisation for recipes and related models (using jason_api adapter)
- Implement error handling for API endpoints
#### Frontend foundation

- Configure React within Rails using js-builder and sprockets
- Set up Tailwind CSS using gem

## The pros and cons of working with Claude.AI

### Strengths
- Speed - this probably would have taken me a couple of weeks by myself, and I would have been doubting some of my choices. Speed plays into almost every other strength as well.
- Explanations - it's so helpful to ask for a comparison of things, or asking for trade offs to try to understand the different options available
- Able to work with things I've never done before - getting set up with a bunch of tools that I've not used before, it was very helpful to be led by the hand with all the boiler plating
- Able to discuss ideas and concerns - it's like having someone to bounce ideas off of, and discuss how current decisions might affect future plans.
- Diagnosing issues - I had quite a lot of difficulty getting the serializer specs to work, which Claude was also having issues understanding. A lot of it came down to my old nemesis, JSON formatting, and something that probably would have stumped me for a while, Claude was able to get me unstuck relatively quickly.

### Challenges
- It makes mistakes - these are really easy to pick up when working in an area I'm familiar with like Rails, but harder to work out in areas that I know very little about. Even when you call it out, it sometimes will only correct part of the file, and not every instance.
- Fighting the laziness and keeping my brain switched on - once you get in the flow, it's tempting to get Claude to do almost everything. However, I want to have ownership over what I'm creating and learn as I go along.
- Ensuring Claude has enough context - previously I was trying to provide context at the start of every chat with summary docs. I recently realised that this is what the "Project" section is for! That has been a significant improvement.
- Becoming over-reliant / Claude's availability - Claude has been having some issues and was erroring out quite a lot and unable to respond. This made me realise how reliant I was becoming when I felt lost and unsure what to do next without Claude available. I think I probably need to get a better sense of the overall direction of things so that I can try to carry on by myself when Claude is down. I think this is mostly a problem in the setup stage, but is something to keep an eye on.

## Next steps

### Learning more
I'm working my way through [Josh Comeau's 2 courses](https://courses.joshwcomeau.com/) "CSS for JS developers" and "The Joy of React" and think I need to get a bit further through them before continuing with coding so that hopefully I can spot when Claude starts leading me astray, and be more independent.

As a small aside, I can't recommend these courses highly enough for a good learning foundation. They are on the more expensive side of things, but the production quality is incredible, and there is a LOT of material explained in a very accessible way, with some great little workshops and quizzes to keep you engaged. My CSS skills are patchy at best, mostly because I've picked up bits and pieces as needed and relied on generous co-workers to help me out. Going through this CSS course I am FINALLY starting to properly understand the rules of CSS and why certain things only work at certain times.

### Wireframes
I think the time has come when I really need to start focusing on how this thing will look. I need to put together some wireframes and designs. This is definitely one of the areas that I'm most daunted by - I am sadly not blessed with a designer's eye. I can tell when something looks off, but I never know how to correct it. Hopefully this is something Claude.AI can help with so I'm not completely in the dark.