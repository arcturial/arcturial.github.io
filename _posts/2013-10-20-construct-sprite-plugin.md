---
layout: post
title: Construct 2 Sprite Factory Plugin
---

I am creating a platform shooter game in Construct 2 and realized that if I want multiple bullet types to spawn based on power-ups, I would have to create quite an elaborate IF statement and sub conditions to spawn the right bullet type based on my current power-up status. I thought it would be much easier to use if I had some sort of **BulletFactory** and instead of spawning the actual **BulletType**, I can instead spawn the instance of the factory and the factory would be aware of which object it’s currently representing. As an example, without this factory plugin. My event sheet would look similar to the image below.

![Without Plugin](http://arcturial.github.io/images/WithoutPlugin.jpg "Image 1")

As you can see, we have to keep track of the type of bullet using a global variable. Then we have sub-events to determine which bullet to spawn. In a game with many types of power-ups this can get elaborate quite fast. In the image below you can see an example of the same logic applied using the object factory plugin.

![Plugin](http://arcturial.github.io/images/Plugin.jpg "Image 2")

It’s much simpler and cleaner. Our **“spawn”** logic remains the same regardless of the bullet type being spawned. This way, we can change the object on the factory without having to worry about global variables or multiple conditions being true for a spawn event to occur. This also makes adding new bullet types trivial. Feel free to drop a comment below if you have any suggestions or questions about the use or installation.

**Project Reference:** [Factory.capx](http://arcturial.github.io/downloads/Factory.capx)