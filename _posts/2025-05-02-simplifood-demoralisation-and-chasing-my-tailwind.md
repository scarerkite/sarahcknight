---
layout: post
title: "Simplifood - demoralisation and chasing my Tail(wind)"
date: 2025-05-02
categories: [Web Development, Simplifood]
tags: [side-project, react, tailwind, ai-assisted, learning, debugging, css]
---

I am starting to feel a bit overwhelmed by the project, and worrying about how much of the work I'm letting Claude.AI do, vs my own understanding. (This post has also been sitting around for a couple of weeks). I want to learn React as I go along, but am also really, really keen to make some visible progress on the app, so am feeling conflicted.

Leaning on Claude quite heavily, I made a start on pulling actual data from the API, and rendering it in my React components. This involved creating a custom hook and then passing the data through.

This is a diagram of my progress so far: 

![Simplifood Flow](assets/images/simplifood_flow.png)

This felt like great progress, although I have a bit of catching up to properly understand how the service and hook work together. 

I did some more learning on my React course and got a bit lost...

So I decided to take a break from React and do a bit of Tailwind dabbling. A decision that I soon regretted! I thought I could knock out a bit of CSS and make everything look a bit prettier than the text list it currently is:

![Unstyled Recipe List](assets/images/basic_data_react.png)

With Claude's help, I added some styles to my components.
I went to check on them locally, and absolutely nothing had changed. 

## Despair

I consulted with Claude, and after a lot of back and forth, found some discrepancies with my file structure and set up. I think relying on AI for the boilerplating is all well and good up to a point... but I hadn't really been paying attention, and it seemed to have applied a mix of methods / philosophies. I think the fact that I'm using the Tailwind gem, rather than Yarn has confused matters. It also doesn't help that with each conversation, Claude is starting from scratch, so sometimes makes recommendations that conflict with previous implementation. 

To provide more context, I've now installed "tree" with Homebrew, and am using that to generate an up to date file structure, and will keep that updated in my project area so that Claude has a bit more context and can see progress to date.

However, that still didn't fix the issue with the styles not appearing.

Then began the most frustrating series of conversations with Claude to date, where we would check the contents of various files, and check that things are in the right order. We would check the results of the CSS in the inspect tab. It would warn me that "Long chats cause you to reach your usage limits faster." so I would start a new chat. And we would start the same checks again.

I started to get more efficient and ask Claude to generate a summary, that I could bring with me to the next chat, which was somewhat successful, but we were still often rechecking the same files. Sometimes for slightly different wording. I started to feel a bit like I was trying to play one of those restrictive "Text Adventure" games from the 90s where you type instructions and go through a series of doors and tunnels, (with a lot of "I don't understand that") and feel like you're making extremely slow progress, but ultimately find yourself back in the same cave that you started in. Even Claude got tired of it, and said it wouldn't speak to me for another 3 hours. (I had apparently exhausted my usage with too many screenshots of errors, or non-errors, but I think that Claude just got sick of me).

## Hello, old friend (darkness ... or Stack Overflow?)

So I ended up back on good old Stack Overflow. It was an infuriating problem - the styles were there in the inspect panel, but not affecting anything. Other people reported similar issues, but the things that helped them, didn't seem to work for me.

After a bit more back and forth, of trying various things, I established that loading the Tailwind styles directly from Tailwind's CDN worked. I had style(s)! This meant that something was failing in the build process locally. Claude reasoned that the most likely reason was the Mac Silicon architecture being incompatible with the way the gem was pre-compiling things. Loading them directly, resolved the issue, but this means that you end up with a lot of styles that you don't need, as it loads every single one. Whereas doing a more tailored build process allows Tailwind to just pick out the styles that you've used.

So we scrapped the gem, and tried using Yarn again.

## Success!

And finally, FINALLY, it worked.

![Basic Styles](assets/images/basic_styles.png)

There are still some tweaks to be made, but I'm delighted to finally have some styled components! That was an extremely painful process though, and took about 2 days to resolve.

I have a suspicion that it was some unfortunate combination of using the wrong file structure / syntax, and not necessarily my computer that was the issue. At some point I discovered that there were quite significant changes between Tailwind 3 and 4, and that most of my syntax seemed to match 3. I asked Claude about this, and it cheerfully admitted that it didn't have access to the latest Tailwind documentation as it hadn't been updated since it came out.

Out of curiosity, I created a super basic Rails app, and was able to get the Tailwind styles to render on that, so I don't think it's anything to do with my local hardware. More likely something clashing in the build process or the config being wrong due to differences between 3 and 4. However, I don't actually mind not using the gem, so will carry on as I am now. It was a good reminder that I should be doing a bit more of my own research though, to avoid these types of mess.

I've also noticed that I seem to be getting less access to Claude than previously. Conversations seem to be running out faster, and I'm getting the "You've completely run out of messages" a lot quicker. I'm not sure if this is trying to push more people towards the "Max" subscription, but it's a bit frustrating. On the plus side, it is making me a bit more strategic with how I use Claude, which can only be a good thing.

## Next steps

- Fix the obvious issues - why are the minutes showing up as "undefined"? Where is the complexity icon?
- I keep seeing TypeScript come up in Job descriptions, so am thinking of migrating over to that to learn more
- Move onto rendering the individual recipes