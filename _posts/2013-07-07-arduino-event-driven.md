---
layout: post
title: Arduino Event-Driven Development
old_url: http://www.cainsvault.com/arduino-event-driven-development/
redirect_from:
    /2013/07/07/arduino-event-driven.html
---

First off, I only recently started experimenting with Arduino and C++. For the last couple of years I have been mostly involved with languages such as PHP, so it’s a rather big change going from a dynamic language to a structured language such as C++.

For my current Arduino project I am attempting to create a remote control car that will have headlights, steering and off course…a drive-train. Having written an article about [“Event Management”](http://deathwing/git/site/wordpress/design-patter-php-event-dispatcher/) in PHP quite recently, I thought that this will be a great pattern to use in my Arduino sketch. I have all these different components to control and instead of doing it with a bunch of if statements in my `loop()` function, I opted for an event-driven approach.

To accomplish this, I wrote a new library for Arduino. It’s simple called **“Event”**...and can be downloaded from [Github](https://github.com/arcturial/arduino-event). I will be using this library as the basis for my examples.

What I want to accomplish in this article is to set up events to turn a LED on and off much like I did in my article explaining on how to use the [Serial Port](http://deathwing/git/site/wordpress/controlling-a-led-using-arduino-serial-port/).

{% highlight cpp linenos %}
#include "Event.h"

EventManager evtManager;
int ledPin = 12;

// Set up the LED task
struct LEDListener : public EventTask
{
    using EventTask::execute;

    void execute(Event evt)
    {
        digitalWrite(ledPin, HIGH);
    }

} LEDListener;


// Run the sketch
void setup()
{
    pinMode(ledPin, OUTPUT);
    evtManager.subscribe(Subscriber("led.on", &LEDListener));
}
{% endhighlight %}

To create a new listener, we simple extend the `EventTask` struct and override it’s `execute` method. For this example we are manipulating an LED, so we can either define the pin and the output in the `LEDlistener` struct or define it in the setup (like we did in the example). When writing your own sketch you will probably have a library to deal with LED’s, instead of defining the pins and modes in your root sketch file.

Now that our listener is a subscriber to the required event, we actually need to **trigger** this event.

{% highlight cpp linenos %}
...

void loop()
{
    // Trigger led event
    Event ledEvent = new Event("led.on");
    evtManager.trigger(ledEvent);
}
{% endhighlight %}

For the purpose of this example, we are going to trigger the LED event in the `loop()` function. You probably shouldn’t trigger an event that does the same thing on every `loop()` cycle. ;)

Regardless, the LED should turn on when you fire up this sketch. If it doesn’t, feel free to drop a comment below and I will try to assist. So…before we conclude this article, let’s have a look at how we can easily make a **“Blinking LED”** program without using any `delay()` commands.

{% highlight cpp linenos %}
#include "Event.h"

EventManager evtManager;
int ledPin = 12;
boolean ledOn = false;

// Set up the LED task
struct LEDListener : public EventTask
{
    using EventTask::execute;

    void execute(Event evt)
    {
        if (ledOn == true)
        {
            digitalWrite(ledPin, LOW);
        }
        else
        {
            digitalWrite(ledPin, HIGH);
        }
    }

} LEDListener;


// Run the sketch
void setup()
{
    pinMode(ledPin, OUTPUT);
    evtManager.subscribe(Subscriber("led.on", &LEDListener));

    Event toggleLed = Event("led.on");
    evtManager.triggerInterval(TimedTask(2000, toggleLed));
}

void loop()
{
    evtManager.tick();
}
{% endhighlight %}

The important changes to note, is the use of `triggerInterval()`. This tells the code to trigger a certain event every X milliseconds (2 seconds in this case). You also need to ensure to call `tick()` on the `EventManager` for every `loop()` cycle. This tells the `EventManager` where in time it is. Without calling `tick()`, timed events won’t work.

If you encounter any problems with the code examples or think you can contribute, please feel free to comment below or fork it on [Github](https://github.com/arcturial/arduino-event).