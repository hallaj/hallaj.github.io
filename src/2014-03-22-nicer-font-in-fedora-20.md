---
title: Nicer font in Fedora 20
date: 2014-03-22
tags:
  - crunchbang
  - fedora
  - infinality
  - openbox
---

Okay, to start this off - I'm actually a [Crunchbang](http://crunchbang.org "Crunchbang") user. Recently my laptop's hard disk died and while waiting for a replacement, I decided to revive an older laptop of mine and give [Fedora](http://fedora.org "Fedora") a try.

I would say that everything was perfect, well - "almost" but I simply hated the font! That prompted me to go on a journey to get the font fixed.

Browsing around and I start to notice that I was not the only one with issues. There are tons of complaints out there about the font rendering on Fedora. I did however found one that was promising, and I gave it a try.

```shell
sudo rpm -Uvh http://www.infinality.net/fedora/linux/infinality-repo-1.0-1.noarch.rpm
sudo yum -y install freetype-infinality fontconfig-infinality libXft-infinality
cd /etc/fonts/infinality/
sudo ./infctl.sh setstyle linux
sudo fc-cache -fv
```

Once, I've done all of those and fired up a new terminal, the font was just lovely! Exited my Firefox browser and there you have it, my Gmail is back to it's beautiful self!

Oh, of course I'm still running [Openbox](http://openbox.org "Openbox") with it :)
