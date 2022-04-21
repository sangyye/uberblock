---
layout: post
title: "Remove Snap from Ubuntu 22.04"
date: 2022-04-21 19:00:00
published: True
categories: [linux]
tags: [ubuntu]
---

Today [Ubuntu 22.04 was released](https://ubuntu.com/blog/ubuntu-22-04-lts-released) and I would like to not use the snap firefox version.
So I decided to remove snap and install firefox via apt again.

What I did after updating to 22.04:
```
% sudo snap list # check what is there and remove it
% sudo snap remove --purge firefox
% sudo snap remove --purge snap-store
% sudo snap remove --purge gnome-3-38-2004
% sudo snap remove --purge gtk-common-themes
% sudo snap remove --purge core20
% sudo snap remove --purge bare
% sudo snap remove --purge snapd # do this last!
% sudo apt purge snapd gnome-software-plugin-snap # the last one is not always there, but can't harm
% sudo apt-mark hold snapd # prevent reinstallation
```

🎉  Now snapd is no longer installed, but oh, snap, you also have no firefox anymore. Time to change that.

```
% sudo add-apt-repository ppa:mozillateam/ppa
% sudo apt install -t 'o=LP-PPA-mozillateam' firefox
% cat << EOF >> /etc/apt/preferences.d/mozillateamppa
Package: firefox*
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 501
EOF
```

Now you have removed snap and installed firefox via the official Mozilla PPA.   
Have fun.
