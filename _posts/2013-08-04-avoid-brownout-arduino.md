---
layout: post
title: How To Avoid Arduino Resetting Due To Current Loss
redirect_from:
    /2013/08/04/avoid-brownout-arduino.html
---

I recently ran into a problem with my [Arduino powered remote control car](https://www.youtube.com/watch?v=LqzbRwjU4dE), the motors would pull too much current and reset the Arduino. Unfortunately, the space I had on the RC car was limited and I had to make due with a **2200mah** battery that didn’t have enough juice to power the motors and the Arduino. After doing some research and trying a few different techniques, I realized that it would be much easier to just have the motor run on it’s own battery before it causes damage to the Arduino. However, before I came to this conclusion…I tried a technique called **power supply decoupling**. I had some success with this, but unfortunately for long running high current components like DC motors…it’s just not good enough. Decoupling the power supply helps to introduce a more stable flow of current. Without decoupling, your components might be wired like this:

![Current](http://arcturial.github.io/images/current.gif "Image 1")

In this instance, we have 2 components that will cause load on the power supply. When **load 1** is active, it might be pulling a steady flow of current. When **load 2** kicks in (depending on how hungry the component is, it might be a high current component like a DC motor), the current on **load 1** can fluctuate which also leads to varying voltages. This can potentially cause noise on the circuit, which can lead to erratic behavior in some components. In my particular case, it would lead to an Arduino brownout and a restart. One way to solve this, is by making sure that the current remains as stable as possible across the different components. Adding some capacitors close to the power pins and close to the individual components helps the circuit to stay stable. When the high current component kicks in, it can lead to a current spike. The capacitor serves as a reserve during this spike. When the spike occurs, the individual components will use the storage in the capacitor to ensure they have enough power. You can decouple your power supply by wiring your circuit like this:

![Current Decoupled](http://arcturial.github.io/images/current-decoupled.gif "Image 2")

Current Decoupled The size of the capacitors will vary, for my purposes I used a 0.1uf capacitor near the component and a 10uf capacitor near the power pins. For more information on the capacitor sizes and how to calculate them, please check out the [reference article](http://www.thebox.myzen.co.uk/Tutorial/De-coupling.html). This should give your circuit enough backup power to survive a current spike when components switch on and off. If you have found another way to avoid **“brownouts”** when running DC motors and the Arduino off the same power supply, please feel free to share them in the comments below.

**Note:** I am not a qualified electrical engineer. I like playing around with electronics as a hobby. If I am incorrect in my statements or the information supplied, please leave me a comment and I will update the entry. The technique mentioned helped me with my requirements, but if you are having problems with DC motors and Arduino, the better idea would be to power them individually.

[Reference Article](http://www.thebox.myzen.co.uk/Tutorial/De-coupling.html)