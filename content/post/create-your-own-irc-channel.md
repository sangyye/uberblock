---
layout: post
title: Create your own IRC Channel
date: 2014-09-01
published: True
categories: []
tags: [irc]
---

This howto was orginally created by [@renejeschke](https://twitter.com/renejeschke) and me. But most credit go to Rene, you should follow him on twitter ;) I publish this on my blog so some more people get some use out of it and it doesn't get lost in some drive.The IRC server this was written for is irc.mozilla.org, but it should work for every irc server. Feedback is always welcome :)

## General guidelines

Please read this document carefully and don’t hesitate to ask a search engine or twitter for advice if you are unsure. Better be safe than sorry.

In this document, stuff you have to type is written like this:

    /CS HELP

Words in capital letters should not be changed and describe the command and it’s actions. It does not matter if you use capital or small letters for typing these words. Important: There must not be a space before the opening ‘/’ of the command.

Arguments to the commands are written in italics and colored:

    /JOIN #channel-name

So, here channel-name is an argument that you have to replace by the real value.
E.g. if you want to create a channel called ‘myAwesomeChannel’ you would type:

    /JOIN #myAwesomeChannel

## Register your nickname

You can not perform any of the actions in this document without having a registered nickname. How to register your nick is not part of this Howto, so try the searchengine of your choice.

## Getting help from IRC

Help for ChanServ (CS) commands can be obtained with:

    /CS HELP

The same works for NickServ (NS):

    /NS HELP

Nice to know: The /CS and /NS commands are shortcuts for /msg ChanServ and /msg NickServ, so you’re in fact sending private messages to those nicknames.

## Create a new channel

We start with creating a new channel called “channel-name” by typing:

    /JOIN #channel-name

E.g. if you want to create a channel called ‘myAwesomeChannel’ you would type:

    /JOIN #myAwesomeChannel

The user that creates this channel is the first to join and is channel operator by default. If you are not the operator then maybe someone has already taken the channel, if he’s not your friend from twitter you have to think of a better channelname.

## Register the channel

To register the channel use:

    /CS REGISTER #channel-name password description

E.g. if I want to register ‘myAwesomeChannel’ with a ‘abc123’ as the password and ‘My awesome channel!’ as the description I would type:

    /CS REGISTER #myAwesomeChannel abc123 My awesome channel!


Be sure to remember the password if you lose the password the channel will be lost if something happens to your account.
The description should be short summary of what the channel is for.

Now you are the founder of this channel and it is registered to your nickname. You now also have total control over this channel and no one else can use this channel’s name.

## Restricting access to the channel

To restrict access to the channel use:

    /CS SET #channel-name RESTRICTED ON
    /CS SET #channel-name SECUREOPS ON

To further prevent the channel from appearing on public lists:

    /CS SET #channel-name PRIVATE ON
    /CS SET #channel-name MLOCK +s

Now, you are the only one that can enter the channel.

## Allowing other users to join the channel

To allow a user called nick-name to enter the channel after restricting access, use:

    /CS HOP #channel-name ADD nick-name

This will add the given user to the half-operator list which automatically allows this user to enter the channel.

## Registering additional administrators for the channel

Now it is time to specify a representative for channel operations (administrator):

    /CS SOP #channel-name ADD nick-name

This adds the given nick-name to the SUPER-OPERATOR list. The person having this rights is the direct representative of the founder and can add/remove people from the access lists. Be sure you have at least one additional SOP per channel!

## Removing all bans in a channel

If a user tries to enter with an unregistered nickname, or while the name is not yet identified, ChanServ will kick and ban the user from the channel.

The easiest way to remove all of those bans is using the following command:

    /CS CLEAR #channel-name BANS

This will remove all bans for the given channel.

## Making sure the channel lasts

You should also specify a successor to make sure the channel does not get lost when your nickname expires:

    /CS SET #channel-name SUCCESSOR nick-name

The given user will now be the successor to the founder of this channel. If anything happens to the founder’s nickname, the successor automatically becomes founder.
