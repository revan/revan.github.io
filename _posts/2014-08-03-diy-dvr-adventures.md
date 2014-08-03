---
layout: post
title: "DIY DVR Adventures"
---
I decided to finally replace my family's pitiful set-top DVR.

## The Past
Our entry into the DVR world was with the [TiVo Series 2](http://www.amazon.com/TiVo-TCD649080-80-Hour-Digital-Recorder/dp/B000ER5G58), a chunky box that interacted with the cable box via a taped-on IR emitter. It had inside two cable tuners, one of which was connected to the cable box and the other directly to the cable, providing the ability to watch two channels at once: one under 100 (unencrypted), and the other any channel.

The TiVo box sold for ~$200, but also required a $15 monthly subscription to receive upcoming TV schedules.
There was an option for a lifetime service plan for several hundred dollars, but that was pretty clearly a terrible deal considering this box didn't even do HD.
In retrospect, that's an exorbitant monthly fee considering scraping work being done, and the free alternatives schedule services maintained by volunteers, but those concerns were quickly quelled by cries of "OH MY GOD, I CAN PAUSE LIVE TV!"

This all worked out alright for a few years until Optimum, in what I can only assume was part of an ongoing campaign to undercut the KGB's customer service ratings, decided to begin encrypting *all* channels, even the lower numbered ones.
This meant that of the TiVo's two-tuner design, the straight-from-cable tuner was now useless, recording only a stern warning that cable box is needed.
From a software point of view, we could then disable the unencrypted tuner and settle for only recording one show at a time.
Enter TiVo's brilliant software design: there was no way to disable the tuner, and the software would try to optimize recording logistics by assigning the unencrypted tuner wherever possible.
In the end, then, the TiVo lost the ability to record any channel below 100. Goodbye, Comedy Central!

Understandably, this situation wasn't to be expected years prior when the software was designed, so it's somewhat unreasonable to expect configuration options to address this issue.
What I did expect, though, was for some sort of hotfix to be pushed: this is a pretty expensive service we're paying for, after all.
No fix came, and the box was dead in the water with no way for community support.

Not eager at the thought of having another TiVo box obsoleted down the road, I decided to try out the cable provider's own DVR solution, an upgrade costing an additional $8 per month with no hardware purchase requirement.
Despite its lumbering Windows 95 monstrosity of a UI, it got the job done and for relatively cheap.
It was pretty barebones, so I supplemented it with a [Raspberry Pi running XBMC](http://www.raspbmc.com/), which streamed files from a desktop on LAN.

I decided there must be a more elegant solution, and decided to build my own DVR.
I found the [Ceton InfiniTV 4](http://cetoncorp.com/products/infinitv-4-pcie/), a PCI-e expansion card for desktops promising four cable tuners, for the sizeable sum of $200.
Still, less than the price of a TiVo, and no recurring fees.

## OS
For the operating system, I was excited to try [MythTV](http://www.mythtv.org/), an open source DVR software that has plugins for cool stuff like automatically detecting and skipping commercials, and of course runs on Linux.
Optimum, never one to disappoint, rained on that parade by setting all their channels to ["Copy Once"](http://en.wikipedia.org/wiki/Copy_Control_Information) rather than "Copy Freely", coating every channel with thick slops of DRM.
Essentially, content is encrypted in such a way that only approved software can decrypt it.
Gaining approval for a software in this case is a multi-thousand dollar certification process consisting of proving that the software abides by the requested DRM restrictions, in this case only allowing a single copy of each program.
Being an open source project, MythTV can't get certified for this unlike Microsoft or TiVo, so none of my channels would be watchable.
So, I was forced into Windows Media Center.
Luckily, Rutgers students get free copies of Windows through [Dreamspark](https://www.dreamspark.com/).

## The Build
I had a 2005 Dell desktop that I had upgraded with a GPU a few years back, so I decided I'd toss the card in there and be done with it.
Setup went fine until it came time to install the Ceton drivers, at which point the system crashed.
After some super fun troubleshooting, including getting a license for a different version of Windows, I learned from Ceton support that the old nForce chipset on the AMD motherboard was incompatible.
Bummer.
The best part about this situation was that there was no upgrade path for this machine: using a rare BTX formfactor, replacement motherboards are hard to come by, and upgrading the motherboard would mean replacing the CPU, RAM, and PSU at least.

## The Build 2
I decided to start fresh:
<table><thead>
<tr>
<th align="left">Type</th>
<th align="left">Item</th>
<th align="left">Price</th>
</tr>
</thead><tbody>
<tr>
<td align="left"><strong>CPU</strong></td>
<td align="left"><a href="http://pcpartpicker.com/part/amd-cpu-ad640kokhlbox" rel="nofollow">AMD A6-6400K 3.9GHz Dual-Core Processor</a></td>
<td align="left">$64.24</td>
</tr>
<tr>
<td align="left"><strong>Motherboard</strong></td>
<td align="left"><a href="http://pcpartpicker.com/part/msi-motherboard-a78me35" rel="nofollow">MSI A78M-E35 Micro ATX FM2+ Motherboard</a></td>
<td align="left">$52.99</td>
</tr>
<tr>
<td align="left"><strong>Memory</strong></td>
<td align="left"><a href="http://pcpartpicker.com/part/kingston-memory-hx318c10frk28" rel="nofollow">Kingston Fury Red Series 8GB (2 x 4GB) DDR3-1866 Memory</a></td>
<td align="left">$72.99</td>
</tr>
<tr>
<td align="left"><strong>Storage</strong></td>
<td align="left"><a href="http://pcpartpicker.com/part/western-digital-internal-hard-drive-wd10ezex" rel="nofollow">Western Digital Caviar Blue 1TB 3.5&quot; 7200RPM Internal Hard Drive</a></td>
<td align="left">$57.99</td>
</tr>
<tr>
<td align="left"><strong>Case</strong></td>
<td align="left"><a href="http://pcpartpicker.com/part/cougar-case-spike" rel="nofollow">Cougar Spike MicroATX Mini Tower Case</a></td>
<td align="left">$15.00</td>
</tr>
<tr>
<td align="left"><strong>Power Supply</strong></td>
<td align="left"><a href="http://pcpartpicker.com/part/corsair-power-supply-cs450m" rel="nofollow">Corsair CSM 450W 80+ Gold Certified Semi-Modular ATX Power Supply</a></td>
<td align="left">$54.99</td>
</tr>
<tr>
<td align="left"><strong>Operating System</strong></td>
<td align="left"><a href="http://pcpartpicker.com/part/microsoft-os-fqc04649" rel="nofollow">Microsoft Windows 7 Professional SP1 (Dreamspark) (64-bit)</a></td>
<td align="left">$0.00</td>
</tr>
<tr>
<td align="left"><strong>Other</strong></td>
<td align="left">Ceton InfiniTV 4</td>
<td align="left">$200.00</td>
</tr>
<tr>
<td align="left"></td>
<td align="left"></td>
<td align="left"><strong>Total</strong></td>
</tr>
<tr>
<td align="left"></td>
<td align="left"></td>
<td align="left">$518.20</td>
</tr>
</tbody></table>

I picked the AMD processor because of its low price and its powerful integrated graphics.
The high speed RAM is necessary for effective use of the graphics power.
The hard drive is cheap but fast enough to keep up with recordings.
The case was on sale, the motherboard fit the pieces, and the PSU fed everything fine.
I chose Windows 7, not because of fanatical anti-8-ism, but because of the free Media Center availability.

![build complete](/public/dvr.png)

## The Setup
This time around, driver installs went without issue.
Setting up the InfiniTV was straightforward; it pops into any PCI-e slot in the motherboard, then takes two inputs: a coaxial cable, and a Multi-Stream CableCARD rented from the cable provider for $2 a month, the same as a modern TiVo.
Note that the coaxial cable is the raw encrypted cable -- no more need for a standalone cable box.

When I opened Windows Media Center, though, I had an unpleasant surprise: after running some diagnostics, it bluntly informed me that the system was not compliant with the minimum requirements for watching live TV.
Confused as to what purchasing mistake I might have made, I ended up realizing that I had forgotten to install the drivers for the AMD integrated GPU; it was trying to render the video using only the CPU.

Oops.

With that fixed, the rest ran fine.
I needed to call the Optimum number onscreen for activation, where I was put on hold listening to highly lossy smooth jazz periodically punctuated by an overeager recording telling me to check out their cool website, only to interrupt himself to remind me to change the batteries in my remote if it's not working, or some similar drivel.

After years of mediocre software, Windows Media Center is a nice step up.
A responsive interface, Netflix, a graphical movie listing, and the ability to play files from disk combine to make a nice experience.
Since the device even has a decent GPU, it can fluidly fastforward TV while pitch-shifting the audio rather than do the spotty slideshow fastforward of my previous devices.
Watching the news at 1.33x speed is cool, and keeps things fresh.

It even offered to scan through all the channels and detect which ones were received, hiding the rest.
Great! Except...

### Tuning Adapter
The staple of my parents' TV diet, a high-numbered French movie channel, was not available.
This channel, and others around it in the channel listing, are less popular so using [Switched Digital Video](http://en.wikipedia.org/wiki/Switched_video) Optimum is able to save bandwidth by only sending the signal to customers actively trying to watch the channel.
Regular cable boxes handle SDV requests behind the scenes, but for other devices, you need a standalone "Tuning Adapter", a little black box which is installed between the InfiniTV and the coaxial cable, relaying requests for SDV channels to the cable provider.

Thankfully, this is offered free of charge, but alas the device is flaky as hell and needs regular rebooting, sometimes failing to switch channels.

## Thoughts
This project ended up going way over budget, though still comparable in price to purchasing a new TiVo with service for a couple years.
If you find yourself with a recent, decently powerful computer lying around, this project might be more cost effective.

The biggest lesson from this adventure for me is that Optimum is a miserable lot, but probably the same as most cable providers.
The layers of unnecessary difficulty they imposed make me unsure of how solid a decision this was.

I might get lucky and turn out to just have a faulty tuning adapter, but this could be a real pain.

Of course, the best course of action when it comes to TV would be to cut the cable entirely.
