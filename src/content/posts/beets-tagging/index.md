---
title: 'Music Tagging with Beets: Making Sense of Your Collection'
published: 2025-10-26
draft: false
description: 'How to set up Beets with Docker to automatically tag, organize, and clean up your music collection. No more "Unknown Artist" nightmares.'
tags: ['Music Tagging', 'Beets', 'Docker', 'Music Organization']
---

Let's talk about the thing that separates serious music collectors from casual listeners: proper tagging. If you've been collecting music for years, you know the pain. Files named "track01.mp3," albums split across multiple artists because of inconsistent spelling, missing album art, wrong release years - it's a fucking nightmare.

I've been dealing with this chaos for 20+ years. Started with mix tapes where you didn't even know track names, went through the Napster era where files were tagged by drunk teenagers, survived the torrent years where album art was a luxury. Now I've got this insane mix of formats and sources, and somehow it all needs to work together in my streaming setup.

That's where **Beets** comes in. Think of it as having a music librarian who never gets tired, never makes mistakes, and has access to the world's biggest music databases.

## Why Beets Instead of Manual Tagging?

Look, I've tried doing this manually. Spent weekends going through folders, fixing artist names one by one, hunting down album art. It's soul-crushing work, and you'll never finish if you've got a serious collection.

Beets connects to MusicBrainz and Discogs - massive databases with millions of releases. When it finds your files, it doesn't guess what album it is. It goes out and grabs the correct information: proper artist names, release years, track listings, album art. Even handles the weird edge cases that would drive you insane.

**The difference is night and day:**
- Before: "Various Artists - Unknown Album - track01.mp3"  
- After: "Minor Threat - Complete Discography - 01 Filler"

And it does this automatically for thousands of files. No more typing artist names, no more hunting for album covers, no more inconsistent formatting.

## My Docker Setup

I'm running Beets in Docker because, like everything else in my setup, I want it contained and manageable. Here's my `docker-compose.yml`:

```yaml
services:
  beets:
    image: lscr.io/linuxserver/beets:latest
    container_name: beets
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - ./config:/config
      - /mnt/nfs_share/jellyfin/music:/music
      - /mnt/nfs_share/punkify:/punkify
    ports:
      - 8337:8337
    restart: unless-stopped
```

**What this does:**
- Runs the LinuxServer.io Beets image (they know what they're doing)
- Maps my music directories so Beets can access everything
- Exposes port 8337 for the web interface (yeah, Beets has a web UI)
- Runs with proper user permissions (PUID/PGID)

The volume mapping is key here. I've got my raw music imports in one folder and my cleaned, organized collection in another. Beets takes the chaos and turns it into something beautiful.

## The Configuration That Actually Works

Here's my `config.yaml` - this is what makes Beets useful instead of just another tool that tries to be helpful but gets in your way:

```yaml
plugins: fetchart embedart convert scrub replaygain lastgenre chroma web discogs
directory: /punkify
library: /config/musiclibrary.blb
art_filename: albumart
threaded: yes
original_date: no
per_disc_numbering: no

convert:
    auto: no
    ffmpeg: /usr/bin/ffmpeg
    opts: -ab 320k -ac 2 -ar 48000
    max_bitrate: 320
    threads: 1
    
paths:
    default: $albumartist/$album%aunique{}/$track - $title
    singleton: Non-Album/$artist - $title
    comp: Compilations/$album%aunique{}/$track - $title
    albumtype_soundtrack: Soundtracks/$album/$track $title 
        
import:
    write: yes
    copy: yes
    move: no
    resume: ask
    incremental: yes
    quiet_fallback: skip
    timid: no
    log: /config/beet.log

lastgenre:
    auto: yes
    source: album

embedart:
    auto: yes

fetchart:
    auto: yes
    
replaygain:
    auto: no

scrub:
    auto: yes

replace:
    '^\.': _
    '[\x00-\x1f]': _
    '[<>:"\?\*\|]': _
    '[\xE8-\xEB]': e
    '[\xEC-\xEF]': i
    '[\xE2-\xE6]': a
    '[\xF2-\xF6]': o
    '[\xF8]': o
    '\.$': _
    '\s+$': ''

web:
    host: 0.0.0.0
    port: 8337
```

Let me break down the important bits:

### Plugins That Matter

**fetchart & embedart** - Automatically downloads and embeds album art. No more staring at generic music note icons.

**scrub** - Removes junk metadata that accumulates over the years. Your files come out clean.

**lastgenre** - Adds genre information from Last.fm. Useful for organization.

**chroma** - Audio fingerprinting for when metadata is completely wrong.

**discogs** - Connects to Discogs database for those rare pressings that MusicBrainz doesn't know about.

### File Organization

The `paths` section is where the magic happens. This tells Beets how to organize your files:

- **default**: `$albumartist/$album%aunique{}/$track - $title`
- **singleton**: Handles single tracks that aren't part of albums
- **comp**: Special handling for compilations
- **albumtype_soundtrack**: Keeps soundtracks separate

This creates a consistent structure like:
```
Minor Threat/Complete Discography/01 - Filler.mp3
Crass/The Feeding of the 5000/01 - Asylum.mp3
```

### Character Replacement

The `replace` section handles characters that break filesystems or look ugly:
- Converts special characters to safe alternatives
- Removes leading dots and trailing spaces
- Handles international characters properly

This prevents those annoying "file not found" errors when some tool can't handle weird Unicode characters.

## How I Actually Use It

### Initial Import

When I get new music (vinyl rips, downloads, whatever), I dump it in my import folder and run:

```bash
docker exec -it beets beet import /music/new_stuff
```

Beets scans the files, matches them against its databases, and asks for confirmation when it's not sure. I can review each match and tell it when it's wrong.

### The Web Interface

The web interface at `http://your-server:8337` is actually pretty useful. You can:
- Browse your tagged collection
- Search for specific tracks or albums
- Play music directly (if you want)
- Monitor import progress

It's not as pretty as Navidrome, but it's perfect for managing your tagging workflow.

### Handling Problem Files

Some files are just fucked. Wrong metadata, corrupt tags, weird encodings. For these, I use:

```bash
# Force a manual match
docker exec -it beets beet import -t /path/to/problem/album

# Check what Beets thinks about a file
docker exec -it beets beet info /path/to/file.mp3

# Re-tag existing files
docker exec -it beets beet update
```

## The Reality of Music Tagging

Here's what nobody tells you: this isn't a one-afternoon project. If you've got thousands of tracks collected over decades, it's going to take time. But here's the thing - once it's done, it's done.

**What to expect:**
- Most mainstream stuff gets tagged automatically
- Obscure releases need manual help
- Live bootlegs and demos are always a pain
- You'll discover duplicates you forgot you had

**But the payoff is huge:**
- Your music server actually looks professional
- Searching actually works
- Album art everywhere
- Consistent naming across your entire collection

## Integration with Navidrome

Once Beets has done its thing, your music works perfectly with Navidrome. Clean metadata, proper album art, consistent artist names - everything just flows together.

I point Navidrome at my `/punkify` directory (where Beets puts the cleaned files), and it's like having a commercial music service, except it's all my music, organized exactly how I want it.

## Tips for Success

**Start small** - Don't try to import your entire collection at once. Start with a few albums, get comfortable with the workflow.

**Review matches** - Beets is usually right, but when it's wrong, it's really wrong. Pay attention during imports.

**Backup first** - Seriously. Beets can modify your files. Have backups.

**Use the logs** - When something goes wrong, check `/config/beet.log`. It's usually obvious what happened.

## The Bottom Line

Beets isn't sexy software. It doesn't have flashy interfaces or clever marketing. But it solves the fundamental problem that drives music collectors insane: making sense of chaos.

After 20+ years of dealing with badly tagged music, Beets is the first tool that actually understands what I'm trying to do. It respects the difference between a studio album and a live recording, between a first pressing and a remaster, between an EP and a single.

Most importantly, it lets me focus on listening to music instead of organizing files. That's what this whole self-hosted streaming thing is about - taking back control so you can actually enjoy your collection.

Your music deserves better than "Unknown Artist." Beets makes sure it gets the respect it deserves.