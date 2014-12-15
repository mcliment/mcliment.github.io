---
layout: post
status: publish
published: true
comments: true
title: DIY Arduino Christmas tree lights
summary: A detailed explanation of my home made light switcher made with Arduino and a relay shield
date: 2014-12-15 22:06:00 +0100
categories:
- projects
tags:
- diy
- arduino
---
Last year I built a _very simple_ light switch with and Arduino board and a relay shield, I put a video on Youtube buy here is the detailed explanation of the code and the parts used.

<iframe width="768" height="576" src="//www.youtube.com/embed/XrC3mWwxlI0" frameborder="0" allowfullscreen></iframe>

The only drawback is that the Relays have mechanical switching parts and are a bit noisy, specially in the silence of the night. 

I bought the board and the shield on [BricoGeek](ttp://tienda.bricogeek.com/), which is a fine shop from Spain but they can be found anywhere. The other smaller parts were bought at my local electronics shop.

So, these are the required parts:

- [Arduino Uno board](http://arduino.cc/en/Main/ArduinoBoardUno)
- [Relay shield](http://www.seeedstudio.com/wiki/Relay_Shield_V2.0) - I've got v1.3 but for this particular project it's irrelevant.
- Non blinking Christmas tree lights. Mine are LED and connected directly to the mains power.
- Universal power supply. USB 5V supply is not enough to power the Relays, so you need an external supply of 9V for them to work properly.
- Electrical tape (to **cover the USB port** of the Arduino and avoid accidental contact with the AC power damaging the board)
- Two wire electrical cable
- Male and female AC plugs
- Small regular screwdrivers for the relays and maybe other types to mount the plugs

The job is easy, just be careful because mains current levels are dangerous and some parts of the board leave it exposed.

The electrical cable has two wires, just cut one of them and connect one lead to the _COM1_ and the other to the _NO1_. If you have more lights just connect them to _COM2_, _COM3_, _COM4_ and _NO2_, _NO3_ and _NO4_ respectively.

You can also use NCn instead of NOn: NC stands for Normally Closed which means it's connected -closed- when pin is low (0) and disconnected -open- when pin is high. NCn works the opposite.

A detailed explanation of the relay can be found on [Seeedstudio wiki page](http://www.seeedstudio.com/wiki/Relay_Shield_V2.0)

Then, mount the plugs on both sides of the cable, male on one side an female on the other.

Finally put some electrical tape over the Arduino USB connector and mount the Relay Shield on top of the Arduino board.

Finally it's time to program the board. On my Github [arduino-christmas-lights](https://github.com/mcliment/arduino-christmas-lights) project you can find the example code.

Basically on the code you can see two state arrays `states1[]` and `states2[]`, one for each connected light. They should be the same size to maintain synchronization between both light strips. On each state array you can define the HIGH/LOW sequences, as long as you want.

Finally there's a loop that turns the relay on and off with a 500ms delay between changes. If you put two equal consecutive states, the relay will maintain it's state.

As you can see it's very simple, but works and opens a world of possibilities. For example syncing the lights with a song adjusting the patterns and the delay to the rhythm.

If you have any doubt just add a comment, drop me a line or add an issue on the Github project.

Merry Christmas and have fun!

