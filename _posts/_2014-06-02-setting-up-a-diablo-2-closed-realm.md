---
layout: post
title:  "Setting up a Diablo 2 Closed Realm"
date:   2014-06-02 00:00:00
categories: 
---

Diablo 2, in my opinion one of the best games ever made. It was released in 2000 and to this day it still has the power to keep me playing for hours.

Thankfully even though its been 14 years Blizard still maintains the realm servers to allow online play, and they are still packed with players, which is a rarety as most online enabled games last only till their successor is released.

In order to maintain the love I have for D2 into the foreseeable future I have decided to setup my own private realm. This is made possible by some fantastic work to devlop [PVPGN](http://pvpgn.berlios.de/) and [D2GS](http://www.pvpgn.pl/d2gs/)

### PVPGN

>PvPGN (Player vs Player Gaming Network) is a bnetd based gaming network server emulation project. It currently supports all Battle.net games (such as StarCraft, Diablo II, and Warcraft III), and most Westwood Online clients (Tiberian Sun, Red Alert 2, Yuri's Revenge). It gives you the power to run your own server, manage your own users, run your own tournaments, etc.

PVPGN comes in both windows and linux flavours, I personally prefer using the linux binary. Although it also has the ability to act a server for Westwood Online games and other Battle.net games I wont cover it in this post.

[Installation Instructions](http://developer.berlios.de/docman/display_doc.php?docid=549&group_id=2291)

[Files (Sourceforge)](http://sourceforge.net/projects/pvpgn.berlios/files/?source=directory)

### D2GS
D2GS is the Diablo 2 game server, it only comes as a windows binary.

For the D2GS installation you will also require all the files from a 1.13d installion of D2 or D2LoD. I just copied the entire contents of the game folder into the D2GS folder.

Instructions for setting up D2GS can be found in a README in the server folder.

[D2GS 1.13c](http://www.pvpgn.pl/d2gs/version_113c/build_3) - This works for 1.13d for me

## Setup 

### Hardware

I am using VM's to construct my realm but physical machines work just as well.

#### Ubuntu 12.04
This will be used to run PVPGN

#### Windows 7
This will run D2GS

### Port Forwarding

The following port forwards are required:

6112 to 6114 - D2CS, D2DBS (PVPGN)
6200 - BNETD (PVPGN)
4000 - D2GS Server
8888 - D2GS Telnet Access
4005 - WWOL (PVPGN)

You dont really need 4005 unless you are planning to use the server to host Westwood Online games.

## Problems

During the setup I ran into a few problems, I've tried to document my solutions below.

### Failed to join game
Problem
When trying to create a new game the process hangs at the cathedral door followed by the message 'Failed to join game'
The problem is that the D2 client is unable to connect to the game server. 

Fix
This can be broken down into a few possible fixes:
1) Address Translation - The realm server (D2CS) did not provide the client with the correct IP to connect to. Check your d2cs.log file and make sure that the correct IP is being sent,if not you need to edit the address_translation.conf file.

2) Port Forwarding - Check port 4000 is forwarding correctly to the game server and not to the realm server.

3) Firewall - Make sure your Windows machine running D2GS isnt firewalling the connections

4) D2GS Not Running - Make sure your D2GS service is running correctly. If it shows as started then check the D2GSSVC.log file in the D2GS folder for any errors. The one that always catches me out is covered seperately below.

### D2GS not running
This is caused by running on a 64bit system, you need to change the locaiton of the registry settings
http://forums.pvpgn.org/index.php?topic=1326.0

### No realms are avaiable right now
Problem
The message "No realms avaiable right now" is displayed after you log into Battle.net

Fix
This meants you havent correctly setup a realm in the realm.conf file (/usr/local/etc/realm.conf), double check the example in the file.

### diablo II was unable to connect to the realm server
-To Add-

### Only users inside / outside the network running the realm can connect
Fix
This caused me a fair few headaches. Firstly you have to play with address_translation.conf to make sure you are giving the correct ip addresses to each of the clients, I found the best way to check this is to watch d2cs.log as users try to connect.

The second, and the way that worked for me, is to setup unique port forwards for each user inside the LAN and then add these to the address_translation.conf file.
