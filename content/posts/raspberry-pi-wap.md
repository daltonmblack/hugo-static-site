<!-- ---
title: "Raspberry Pi - WiFi Access Point"
description: "Custom pass-through access point using a Raspberry Pi Zero 2 W"
date: "2022-01-12"
categories:
tags:
  - "raspberrypi"
  - "wifi"
  - "debugging"
  - "blog"
--- -->

## Intro

This week, I turned a Raspberry Pi into a WiFi "access point" (like your WiFi router at home). I don't believe I've ever completed a project with a Raspberry Pi before this. I think the closest project I built was using an [Arduino](https://en.wikipedia.org/wiki/Arduino) to display photos from an SD card on a small LED screen. I'm excited to have gotten my feet wet with this project and intend to build cooler projects in the coming months.

### What is a Raspberry Pi?

![raspberry_pie](/images/raspberry_pie.gif)

[Raspberry Pi's](https://www.raspberrypi.org/help/what-%20is-a-raspberry-pi/) are incredibly affordable (starting around $15 USD) credit-card sized computers aimed at helping a wide audience learn about computers and programming. There are a many lists of beginner tutorials if you're interested in trying out a Pi for yourself, like [this list](https://pimylifeup.com/category/projects/beginner).

### What is a WiFi access point?

A WiFi access point is a wireless transmitter that allows devices to connect to it via a network name/password, and then provides Internet access to authenticated clients. Home WiFi routers are an example of access points.

## Raspberry Pi WiFi (translation, lots of debugging...)

Right on par with my expectations, this was yet another project that took a LOT of start up effort and debugging. Starting something completely new takes trial-and-error because there's no baseline for what to expect, what pitfalls to watch out for, what is good vs bad behavior, etc. This project fell right into that category.

### Connecting to the Raspberry Pi

The first task to using the Pi for a project, was figuring out how to actually connect to it. I didn't even open my Pi until after I had already flown across the country for a trip with my brother. That's when I realized that the normal way to program a Pi is with a monitor and keyboard, just like a regular computer... duh... Well then, I was off to Googling "how to connect to Raspberry Pi without a monitor?". After starting to dig through results, I learned about the Pi's "headless mode". This allows connecting-to/configuring the device via its USB port via SSH (instead of its mini-HDMI port). At this point, it feels like the right time to insert a picture of the Pi I am working with for reference: [Raspberry Pi Zero 2 W](https://www.raspberrypi.com/products/raspberry-pi-zero-2-w).

![raspberry_pi_zero_2_w](/images/raspberry_pi_zero_2_w.jpg)

Through Googling, I found a few promising tutorials, all with a lot of mutual overlap. This is where I hit my first speed bump.

#### Obstacle #1: Loading the Raspberry Pi OS onto a micro SD

As can be seen in the image above, the Pi has a micro-SD slot for its persistent storage. This is also where the Pi loads its operating system from. Again, due to my lack of preparedness, I found myself across the country with a micro-SD card I needed to load an OS onto, but my laptop only has a regular SD slot. I searched on Google Maps for tech stores nearby, and called places until I found a local repair store where the owner had an adapter he would sell me. I walked over to the store and picked up the adapter.

The rest of loading the OS onto the micro-SD was relatively straightforward using [Raspberry Pi Imager](https://www.raspberrypi.com/software). The only caveat here was doing some research to understand the change in naming of the OS. It was previously "Raspbian", and a lot of tutorials reference that name. Once I felt up-to-speed on that discrepancy, the imager software made it super each to download-then-write the OS to the card.

#### Obstacle #2: Connecting to the Pi using SSH over USB

The next steps in connecting to the Pi involved manually updating some configuration files on the SD card (while the card is still plugged into my actual laptop). In addition to the config file updates, I also created an empty file named `ssh`. Don't worry, if you're interested in more detailed steps, I'll be posting key links in the `Appendix/Resources` section at the bottom.

At this point, I was ready to try things out. I put the SD card in the Pi, and connected the Pi to my laptop via their USB ports. After waiting the requisite amount of time for the Pi to boot up, I attempted to connect to the Pi from my _Windows_ (sigh) laptop using PuTTY to SSH to `raspberrypi.local`. PuTTY threw an error saying it could not find the hostname.

My first thought was that I missed a setup step when configuring the Pi to run SSH over USB. So, I turned off the device and inspected the files on the SD card via my laptop. To my dismay, a new file (named `ssh`) I had created, was now missing. I recreated this file, loaded the card into the Pi, and then tried connecting via SSH. I got the same error. And, upon inspection, the file was gone again.

Back to Google I went. Eventually, I learned that the Pi consumes `/boot/ssh` upon startup, which causes it to enable SSH. After that point, SSH remains enabled until something disables it. Since the missing file wasn't actually a problem, I cross-referenced a few more tutorials, but none of them worked. This caused me to change tactics to connecting over WiFi.

#### Obstacle #3: Connecting to the Pi using SSH over WiFi

During my tutorial exploration, I saw references to it being possible to connect to the Pi via WiFi, by being on the same local network. It's all possible because this little $15 computer also has a WiFi chip in addition to all of its other cool functionality. This step went quite smoothly. I'll get to why I'm considering it an obstacle soon. The crux of enabling this functionality is adding a file named `/boot/wpa_supplicant.conf` and adding the desired WiFi connection information to it:

```
country=us
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
 scan_ssid=1
 ssid="GoodTimeMiami2"
 psk="Pa55w0rd1234"
}
```

After loading the SD card back into the Pi and booting it up, I was finally able to SSH via PuTTY! This was a good milestone.

Now, I cautiously thought I was home-free to turn the Pi into an access point like I originally intended. I followed the appropriate steps, making necessary alterations such as substituting the `usb0` interface for the `eth0` interface because the USB interface is the one I was using in Ethernet mode to connect to my laptop. I completed all of the steps, and then told the Pi to restart. This expectedly killed my SSH connection.

However, once the Pi restarted, the new WiFi network (e.g., SSID "GoodTimeMiami2") never appeared. Not only was this a problem, I couldn't re-SSH to the Pi, because I had repurposed the WiFi interface (`wlan0`) for running the new access point. This is why this section is still considered an obstacle. I needed to circle back to my original attempt of connecting to the Pi via SSH over USB.

#### Obstacle #4: Connecting to the Pi using SSH over USB (again)

If you remember from above, the connection problem was manifesting as a generic PuTTY error about being unable to resolve the hostname. I'll gloss over a lot of the detail here. During a slew of Google searches, I noticed a forum post that referenced a missing Windows driver named `RNDIS/Ethernet Gadget`. This led to another forum post identifying this missing driver as the problem.

Now I was on a quest to track down this driver that, as far as I can tell, had its newest version released in 2010. This scavenger hunt involved multiple obscure forum posts as well as dead links that used to host the missing driver. Eventually, I came across an obscure YouTube video, narrated by a bot, that pointed to a working copy of the driver and also gave instructions of how to get Windows `Device Manager` to correctly install the driver's "cabinet file", which was a file type I had no idea existed until that moment. A bit of fairy dust and a smidge of incantation later, the driver was installed!

I attempted to SSH to the Pi over USB, and it now worked! Huzzah! My biggest problem of the project thus far had been solved. I could actually start working on the entire point of the project: turning a Pi into a WiFi access point.

### Setting up the WiFi access point

This section of the project only had a single road block! I'm going to gloss over most of the detail of setting up the WiFi access point because the debugging aspect of this project feels like where I learned the most.

I carefully implemented all necessary steps, and then restarted the Pi hoping for the best. Sadly, my new network, `GoodTimeMiami2` didn't show up still.

#### Obstacle #5: finding the missing network

Solving this issue involved more... wait for it... Googling and trial-and-error. Eventually, I thought to test the liveness of one of the key services I installed on the Pi, `hostapd`, by running `sudo systemctl start hostapd`. This produced an error, which gave me talking about the service being "masked". This gave me a concrete symptom to refine my search with. After more searching I found a solution for how to unmask the service. The solution worked and I was able to successfully start the service.

With fingers crossed, I opened up my phone to search for the new WiFi network (`GoodTimeMiami2`), and it showed up!

### Results

First is a screenshot of my phone being successfully connected to the new `GoodTimeMiami2` network. It probably isn't a surprise that I made this project while visiting Miami.

![rpi_connected_network](/images/rpi_connected_network.jpg)

Second is a picture of the final Pi setup in front of the connected SSH terminal.

![pi_wifi](/images/pi_wifi.jpg)

## Conclusion

Out of the roughly 6 hours that this project took, probably 5 hours of it were debugging. All of the debugging effort paid off, and I got the Pi up-and-running as a WiFi access point!

The feeling of solving an obnoxious problem after hours of debugging is one I personally find super satisfying. Also, banging my head against a difficult problem is one of the most effective ways for me to learn new things. The process of trying to solve the problem involves poking and prodding it from every angle. This results in me learning a broader set of information, instead of only the N exact steps required to actually solve the problem.

## Appendix/Resources

Given all of the debugging involved in figuring out this project, I wanted to link the resources that helped me overcome all of the obstacles:

* [How to use your Raspberry Pi as a wireless access point (thepi.io)](https://thepi.io/how-to-use-your-raspberry-pi-as-a-wireless-access-point)
  * Main tutorial for turning the Pi into an access point
* [Raspberry Pi Zero USB/Ethernet Gadget Tutorial (circuitbasics.com)](https://www.circuitbasics.com/raspberry-pi-zero-ethernet-gadget)
  * How to connect to the Pi via SSH over USB from Windows
* [Installing Ethernet/RNDIS driver on Windows 10 to connect Raspberry PI via USB (youtube.com)](https://www.youtube.com/watch?v=D1JFnPxR5XQ)
  * Detailed instructions for installing the RNDIS/Ethernet-Gadget driver on Windows, if the driver is missing
  * For instructions on how to share a computer's connection with the Pi via USB, search `SETTING UP SHARED INTERNET ACCESS` in this tutorial
* [RNDIS/Ethernet-Gadget Driver Download (microsoft.com)](https://www.catalog.update.microsoft.com/Search.aspx?q=usb%5Cvid_0525%26pid_a4a2)
* [Failed to start hostapd.service: Unit hostapd.service is masked. (github.com)](https://github.com/raspberrypi/documentation/issues/1018#issuecomment-471335938)
  * GitHub issue which explains how to fix the `hostapd` masking error
