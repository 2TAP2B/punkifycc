---
title: 'Installing Ubuntu 24.04 LTS'
published: 2025-10-23
draft: false
description: 'Step-by-step guide to installing Ubuntu Server on your music streaming hardware, plus essential post-install setup.'
tags: ['Server Software', 'Ubuntu', 'SSH']
---

Alright, you've got your hardware sorted - whether it's an old laptop, a Raspberry Pi, or whatever you managed to dig up. Now comes the fun part: turning that piece of hardware into a proper music server. We're going with Ubuntu Server 24.04 LTS, and I'm going to walk you through every single step.

## Why Ubuntu Server 24.04 LTS?

Before we dive in, let me remind you why we're doing this the boring way:
- **5 years of support** out of the box (10 years with Ubuntu Pro)
- Everything just works - no hunting for drivers
- Massive community - when you Google error messages, you'll find answers
- **No desktop environment** - all your system resources go to streaming music, not drawing pretty windows

## What You'll Need

- Your chosen hardware (laptop, Pi, whatever)
- A USB stick (8GB minimum)
- Another computer to create the installation media
- An ethernet cable (seriously, use wired for the install)
- About 30 minutes of your time

## Step 1: Download Ubuntu Server

Head over to [ubuntu.com/download/server](https://ubuntu.com/download/server) and grab Ubuntu Server 24.04 LTS. Yes, the LTS version - we want the boring, stable, long-term support version.

You'll get an ISO file that's about 2.5GB. While that's downloading, let's prepare your USB stick.

## Step 2: Create Installation Media

### On Windows:
1. Download [Rufus](https://rufus.ie/) - it's free and just works
2. Plug in your USB stick (warning: this will erase everything on it)
3. Open Rufus, select your USB drive
4. Click "SELECT" and choose your Ubuntu ISO
5. Hit "START" and wait

### On Linux/Mac:
Use the `dd` command (be very careful with this - double-check your device names):

```bash
# Find your USB device (probably /dev/sdb or similar)
lsblk

# Write the ISO (replace /dev/sdX with your actual USB device)
sudo dd if=ubuntu-24.04-live-server-amd64.iso of=/dev/sdX bs=4M status=progress
```

## Step 3: Boot from USB

1. Plug the USB stick into your soon-to-be server
2. Boot it up and get into the boot menu (usually F12, F2, or ESC during startup)
3. Select your USB stick as the boot device
4. You should see the Ubuntu installer screen

If you're using an old laptop, make sure it's plugged in - you don't want it dying mid-install.

## Step 4: Ubuntu Installation Walkthrough

Here's where we actually install the thing. The Ubuntu installer is pretty straightforward, but there are a few spots where you don't just hit "Next."

### Language and Keyboard
Pick your language and keyboard layout. If you're reading this in English, you probably want English and a standard US/UK keyboard layout.

### Network Setup
This is important - make sure Ubuntu detects your ethernet connection. If you're using WiFi (please don't), set it up here. You want a working internet connection for the install.

**Don't just hit next here** - verify your network is actually working.

### Storage Configuration
Here's where it gets interesting. You have a few options:

**Use entire disk (recommended for beginners):**
- Select your main drive
- Choose "Use entire disk"
- Set up LVM if you want (I usually do - makes future changes easier)

**Custom partitioning (for advanced users):**
- You can set up custom partitions here
- For a music server, a simple setup works fine: one root partition and swap

**Important**: If your laptop has an existing Windows installation you want to keep, make sure you understand what you're doing here. The installer will show you what it's going to do before it does it.

### Profile Setup
This is where you create your user account:

- **Your name**: Whatever you want
- **Server name**: Pick something meaningful like "musicserver" or "navidrome-box"
- **Username**: Keep it simple - you'll be typing this a lot
- **Password**: Make it secure, but not so complex you'll forget it

**Write these down!** You'll need them to log in.

### SSH Setup
Here's a crucial decision. The installer will ask if you want to install OpenSSH server. **Say yes.** This lets you manage your server remotely, which is incredibly handy.

You can also import SSH keys from GitHub/Launchpad here, but we'll cover setting up SSH keys properly in the next section.

### Software Selection
The installer might offer to install Docker, but skip it for now. We'll install Docker properly later with all the configurations we need.

## Step 5: First Boot and Initial Setup

After the installer finishes, remove the USB stick and reboot. You should see a login prompt. Log in with the username and password you created.

Congratulations - you now have a working Ubuntu server! But we're not done yet.

## Essential Post-Install Setup

Now comes the stuff that keeps your server secure and up-to-date without you having to babysit it.

### Update Everything
First things first - update your system:

```bash
sudo apt update && sudo apt upgrade -y
```

This might take a few minutes. Go make some coffee.

### Enable Unattended Upgrades
This is crucial - you want security updates to install automatically:

```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure -plow unattended-upgrades
```

Select "Yes" when it asks about automatically downloading and installing stable updates.

### Install Essential Tools
Let's install some tools we'll need:

```bash
sudo apt install curl wget git htop nano ufw -y
```

### Configure the Firewall
Ubuntu comes with a firewall (ufw), but it's not enabled by default:

```bash
# Allow SSH (important - don't lock yourself out!)
sudo ufw allow ssh

# Enable the firewall
sudo ufw enable
```

### Install Docker and Docker Compose
Docker makes managing your music server software much easier:

```bash
# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update package list and install Docker
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

# Add your user to the docker group (so you don't need sudo for docker commands)
sudo usermod -aG docker $USER

# Log out and back in for the group change to take effect
```

## SSH Setup (Optional but Highly Recommended)

SSH is what lets you manage your server from anywhere. Instead of sitting at your server typing commands, you can control it from your main computer, your phone, wherever.

### Why SSH Keys?
Instead of typing your password every time, SSH keys are much more secure and convenient. Think of it like having a special key that only you have.

### Step 1: Find Your Server's IP Address
First, you need to know how to reach your server. On your server, run:

```bash
hostname -I | awk '{print $1}'
```

Look for something like `192.168.1.100` or similar - that's your server's IP address. Write it down.

### Step 2: Creating SSH Keys

#### On Windows (PowerShell):
```powershell
# Open PowerShell and run:
ssh-keygen -t ed25519 -C "your_email@example.com"

# Just press Enter for all the prompts (default location and no passphrase for simplicity)
# Your key will be saved in C:\Users\YourName\.ssh\
```

#### On Linux/Mac:
```bash
# Run in terminal:
ssh-keygen -t ed25519 -C "your_email@example.com"

# Press Enter for all prompts
# Your key will be saved in ~/.ssh/
```

### Step 3: How to Connect to Your Server (Password Method First)

Before we set up fancy SSH keys, let's make sure you can actually connect to your server. We'll start with password authentication - it's simpler and gets you connected right away.

#### From Windows:

**Option 1: PowerShell (built-in, easiest)**
```powershell
# Open PowerShell and run:
ssh username@192.168.1.100

# Replace 'username' with your actual username
# Replace '192.168.1.100' with your server's actual IP
```

**Option 2: PuTTY (if you prefer a GUI)**
1. Download [PuTTY](https://www.putty.org/)
2. Open PuTTY
3. Enter your server's IP in "Host Name"
4. Port should be 22
5. Click "Open"
6. Enter your username and password when prompted

#### From Mac:
```bash
# Open Terminal and run:
ssh username@192.168.1.100

# Replace with your actual username and server IP
```

#### From Linux:
```bash
# Open terminal and run:
ssh username@192.168.1.100

# Replace with your actual username and server IP
```

#### Your First Connection

When you connect for the first time, you'll see something like:
```
The authenticity of host '192.168.1.100 (192.168.1.100)' can't be established.
ED25519 key fingerprint is SHA256:some_long_string_here.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Type `yes` and press Enter. This is normal - your computer is just making sure it's really your server.

Then it'll ask for your password. Enter the password you created during Ubuntu installation.

**Success!** You should now see your server's command prompt:
```
username@musicserver:~$
```

Try typing `exit` to disconnect and return to your local machine. Then try connecting again to make sure you've got it down.

### Step 4: Adding SSH Keys (Make Life Easier)

Now that you can connect, let's set up SSH keys so you don't have to type your password every time.

#### From Linux/Mac (The Easy Way):
```bash
# This command copies your key to the server automatically
ssh-copy-id username@your_server_ip
```

That's it! It will ask for your password one last time, then you're done.

#### From Windows (The Manual Way):
Since Windows doesn't have `ssh-copy-id`, we need to do this manually:

1. **Copy your public key:**
```powershell
Get-Content C:\Users\YourName\.ssh\id_ed25519.pub | Set-Clipboard
```

2. **SSH into your server** (using the method from Step 3)

3. **On your server, run these commands:**
```bash
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

4. **Paste your public key** into the nano editor (Ctrl+Shift+V), then save and exit (Ctrl+X, then Y, then Enter)

5. **Set proper permissions:**
```bash
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```

6. **Exit your server** and try connecting again - no password needed!

### Step 5: Test Your Connection

Try a few commands to make sure everything works:

```bash
# Check where you are
pwd

# Check system info
uname -a

# Check available disk space
df -h

# Exit back to your local machine
exit
```

### Troubleshooting SSH

**Can't connect at all?**
- Make sure your server is on and connected to the network
- Check the IP address again: `ip addr show`
- Make sure SSH is running: `sudo systemctl status ssh`

**Connection refused?**
- SSH might not be installed: `sudo apt install openssh-server`
- Firewall might be blocking it: `sudo ufw allow ssh`

**Password not working?**
- Double-check your username and password
- Try connecting from the server itself first: `ssh localhost`

### GitHub Integration (Bonus)
If you have a GitHub account, you can add your SSH key there too. This way, you can pull code repositories directly to your server:

1. Go to GitHub Settings → SSH and GPG keys
2. Click "New SSH key"
3. Paste your public key content
4. Now you can clone repositories with SSH URLs

### Pro Tips for SSH

**Create a shortcut (Linux/Mac):**
Edit `~/.ssh/config` on your local machine:
```
Host musicserver
    HostName 192.168.1.100
    User your_username
    Port 22
```

Now you can just run: `ssh musicserver`

**Keep connections alive:**
Add this to your SSH config to prevent timeouts:
```
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

## Testing Your Setup

Let's make sure everything is working:

```bash
# Check if Docker is running
docker --version
docker compose version

# Check system status
sudo systemctl status ssh
sudo systemctl status unattended-upgrades

# Check available disk space
df -h

# Check system resources
htop  # Press 'q' to quit
```

## What's Next?

You now have a solid, secure Ubuntu server ready to become your music streaming powerhouse. In the next posts, we'll:

- Install and configure Navidrome
- Set up proper storage for your music collection  
- Configure automatic backups
- Add monitoring so you know when something goes wrong

Your server is now ready to rock. It'll keep itself updated, it's secure, and it has all the tools we need to build something awesome.

## Quick Reference

### Key Commands You'll Use:
```bash
# System updates
sudo apt update && sudo apt upgrade

# Check running services
sudo systemctl status service_name

# View system logs
sudo journalctl -f

# Check disk space
df -h

# Monitor system resources
htop
```

### Important File Locations:
- SSH config: `~/.ssh/`
- Docker configs: `/etc/docker/`
- System logs: `/var/log/`

Keep this post bookmarked - you'll probably reference these commands more than you think. Next stop: getting Navidrome up and running!