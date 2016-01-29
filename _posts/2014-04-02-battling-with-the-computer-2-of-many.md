---
layout: post
title: Battling with the Computer - 2 of many
date: 2014-04-02 03:11
author: siddhantj
comments: true
category: tech
---
Another extremely trivial battle I used to have with my computer everyday until I decided to do something about it, finally.

The problem: I have a dual boot system, with both Ubuntu 13.10 and Windows 8 installed. What sucked was that every time I tried to mount the Windows 8 partition, I used to get a nightmarish-ly long error message like this:

"Error mounting /dev/sda3 at /media/siddhant/OS: Command-line `mount -t "ntfs" -o "uhelper=udisks2,nodev,nosuid,uid=1000,gid=1000,dmask=0077,fmask=0177" "/dev/sda3" "/media/siddhant/OS"' exited with non-zero exit status 14: The disk contains an unclean file system (0, 0).
Metadata kept in Windows cache, refused to mount.
Failed to mount '/dev/sda3': Operation not permitted
The NTFS partition is in an unsafe state. Please resume and shutdown
Windows fully (no hibernation or fast restarting), or mount the volume
read-only with the 'ro' mount option."

Of course, I never bothered understanding the message completely and ended up cursing Ubuntu and Windows 8, since obviously, I had "shutdown" my windows properly.

Finally, this time, when a deadline was looming over my head and I had no time to waste, I decided that it was indeed the opportune moment to figure out how to solve this small problem (and right a blog post about it!).

Fortunately, it wasn't that big a deal at all. Apparently, Windows 8 has a feature called "Fast Restart", wherein, you don't actually shutdown, even if you shutdown (such meta, much wow). Instead, you go in a pseudo hibernation phase, where certain files are saved, so as to reduce the amount of time Windows 8 takes to load the next time.

All I had to do was just disable that feature[fast restart] from the control panel and voila, problem solved.
and deadline missed as well. bleh.
