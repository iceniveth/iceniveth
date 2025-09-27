---
title: 'Home Movie Server (Jellyfin)'
description: 'The development environment I make to start building'
pubDate: 'Sept 15 2025'
# heroImage: '../../assets/my-development-setup.png'
---

In our house, my father routine is to watch TV all day. He is a fan of James Bond (the old school). We have a television and he is only the one watching on it.
The television is a budget android TV (~$200) with an internal storage of 4GB. That storage size is too low to fit a ton of movies. The challenge is how to show all the movies I have to the TV.

### Flash Drive Mounted

I've copied the movies into a 128GB flash drive. I've plugged it in to the TV. Sadly, It seems to not find those files. Or just a skill issue of mine or the flash drive or the TV?

### PC hosts the movies

I thought about what if the TV will access the movies from my computer.


#### VLC

A bit hard to setup, did work eventually.


#### Plex

Requires to sign up.
Worked but default configuration causes stress on my PC. Streaming at higher quality to a budget TV doesn't seem to make sense 
The UI is a bit hard to navigate for old people non techie.

#### Jellyfin

This one seems to be the right fit. Can be [downloaded](https://jellyfin.org/downloads/windows/) in different operating system. But I prefer mine in Docker:

```bash
docker run --name jellyfin \
  --restart unless-stopped \ 
  -d \
  -v /srv/jellyfin/config:/config \ 
  -v /srv/jellyfin/cache:/cache \
  -v /mnt/d/Media:/media \ 
  -p 8096:8096 \
  jellyfin/jellyfin:latest
```

The `/mnt/d/Media` is where my movies collection.

When it finished installation, it's accessible via `http://localhost:8096` in the browser.

Client devices can connect to this server via IP address + port.

It's important to retain the IP address of the server so that when the server restarts, it's IPv4 would remain the same. 
Luckily my TP link router provides a way to reserve or set IPv4 address to this machine.

On the television side (client), the need to install the [Jellyfin](https://play.google.com/store/apps/details?id=org.jellyfin.mobile&hl=en) app as well.
For it to connect, it would to know server IPv4 address + port e.g. `192.168.0.X:8096`.

Once connected, there are to ways to login: 
1. via username/password which can be created from the server
2. via Quick connect. A code will be present from the TV, and this code must be inputted on the server. On the server, click the user icon in the upper right corner then proceed with **Quick Connect**. Input the code from the TV.
