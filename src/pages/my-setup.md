---
layout: '~/layouts/MarkdownLayout.astro'
title: My Setup
---

I'm way past the Raspberry Pi starting point now. I've been tumbling down the Linux, open source, free software rabbit hole for about 10 years, and honestly? The only flag I'm holding is my own sovereign homelab flag.

## Beyond the Pi: My Homelab Journey

What started with curiosity about alternatives to Big Tech has turned into hosting pretty much everything myself - mail, password manager, cloud storage, photo backup, and of course, my entire media stack. It's not about being paranoid; it's about being independent.

## Current Hardware Setup

These days I'm running everything on a small mini PC with **Proxmox** (which is Debian under the hood - pure Linux goodness). Proxmox lets me spin up virtual machines for all my different services, keeping everything organized and isolated.

But here's the beautiful thing about Linux: *it really doesn't matter* what hardware you use. Whether it's an old laptop collecting dust, a retired office PC, or that Raspberry Pi we talked about - the setup process is always the same. Linux runs on anything.

## The Software Stack

My music streaming setup runs on **Ubuntu Server** with **Docker**, and the whole thing comes together with just about 20 lines in a configuration file. That's it. No complicated installations, no hunting down dependencies, no vendor-specific nonsense.

Docker containers make everything portable and predictable. If I want to move my setup to different hardware tomorrow, I just copy my config and I'm done. That's the power of open source - your setup isn't locked to any particular company or platform.

## Why This Matters

Ten years ago, I never thought I'd be running my own email server or hosting my own "Spotify." But once you start down this path, you realize how much control you've been giving away. Every service you self-host is one less dependency on companies that can change their terms, raise prices, or just disappear.

The best part? Everything I'm using is free software. No licensing fees, no subscription costs, no "enterprise editions" with the features you actually need. Just solid, community-built tools that respect your freedom.

*Technical details and configuration examples coming soon...*
