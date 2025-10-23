---
layout: '~/layouts/MarkdownLayout.astro'
title: Software Setup
---

# Software Setup

Alright, you've got your hardware sorted. Now let's talk about what actually runs on this thing.

![software](/software.jpg)

## Ubuntu 24.04 LTS: The Boring Choice That Actually Works

I'm going with Ubuntu 24.04 Server LTS, and before you start rolling your eyes about how "mainstream" that is, hear me out. This isn't about being cool or using some obscure Linux distribution that three people have heard of. This is about building something that actually works and keeps working.

**Why Ubuntu Server LTS?**
- **5 years of support out of the box** - Set it up once, forget about it
- **Ubuntu Pro gives you 10 years** - A few clicks and you literally never have to think about this again
- **Everything just works** - Drivers, hardware compatibility, software packages
- **Massive community** - When something breaks, someone else has already fixed it

Look, I know there are a million other Linux distributions out there. Arch users will tell you about how much control you have. Debian purists will lecture you about stability. But you know what? I want to stream music, not spend my weekends recompiling kernels.

## No GUI, No Problem

We're installing Ubuntu Server - that means no desktop environment, no fancy graphics, just a command line. 

"But I don't know command line!" you might be thinking. Well, here's the thing: you're about to learn, and it's actually pretty cool once you get the hang of it. There's something satisfying about controlling your entire music server with just text commands. It's like having a conversation with your computer.

Plus, without all that GUI overhead, your old laptop will run faster and use less resources. More power for actually streaming music instead of drawing pretty windows you'll never look at.

## The Command Line: Your New Best Friend

I get it - the command line looks scary at first. All that black screen and white text feels like something from a 90s hacker movie. But honestly? It's way more powerful than clicking through menus, and once you learn the basics, you'll wonder why you ever bothered with all those mouse clicks.

Don't worry, I'm going to walk you through every single command we need. Copy, paste, done. By the end of this, you'll be able to:
- Navigate your server like a pro
- Install and configure software
- Monitor what's happening
- Fix problems when they pop up

And if you really hate the command line? Fine, we can set up a web interface later so you can click buttons to manage your server. But trust me - give the terminal a chance first.

## What We're Actually Installing

Here's the roadmap for what we're putting on this machine:

1. **Ubuntu 24.04 Server LTS** - The rock-solid foundation
2. **Navidrome** - The music streaming server that actually understands collectors
3. **Docker** - Makes installing and managing everything else stupid simple
4. **Tailscale** (optional) - For secure remote access to your music
5. **Some basic monitoring tools** - So you know when something's wrong

That's it. No bloated media center software, no complicated database setups, no dependencies hell. Just the tools we actually need.

## The 10-Year Plan

With Ubuntu Pro, you get 10 years of security updates. Think about that - set this up once, and it'll keep working until 2034. Your music will still be streaming when flying cars are finally invented (they won't be, but still).

This isn't some hobby project you'll need to babysit every weekend. This is infrastructure. Boring, reliable infrastructure that just works.

## What's Coming Next

In the detailed setup posts, I'll show you exactly:
- How to download and install Ubuntu Server
- The essential commands you need to know
- Setting up SSH so you can manage everything remotely
- Installing Docker and getting Navidrome running
- Configuring everything to start automatically
- Basic troubleshooting when things go sideways

No shortcuts, no "and then some magic happens here" - every single step, explained in plain English.

Ready to turn that old laptop into a music streaming powerhouse? Let's do this.
