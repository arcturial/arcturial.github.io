---
layout: post
title: Controlling a LED Using Arduino Serial Port
fullview: true
---

I recently started a project to build a remote control car powered by [Arduino](http://www.arduino.cc/) and [Raspberry PI](http://www.raspberrypi.org/). I will hopefully be publishing some more advanced [Arduino](http://www.arduino.cc/) tutorials soon, but first…let’s cover some basics. I don’t claim to be proficient in Arduino or C++, so any feedback is welcome in the comment section below.

We will be using the tutorial provided [here](http://arduino.cc/en/Tutorial/Blink?from=Tutorial.BlinkingLED) as a base. Please feel free to check it out for more information.
What do you need?

* **Arduino UNO** (or [any](http://arduino.cc/en/main/boards))
* 1x 5mm LED
* 150 ohm resistor (+/-)
* USB to USB-B cable (to connect the Arduino to the desktop)


Now let’s wire up the LED to the Arduino. We will be using pin **number 13** on the board.

Easy right? Ok, now let’s light it up real quick. If you haven’t installed the [Arduino IDE](http://arduino.cc/en/main/software) by now, please do.

I am not going to talk about setting up your development environment as there’s already some good information available for that. You can go check out the official guide to [getting started](http://arduino.cc/en/Guide/HomePage). I will just assume that your environment is set up correctly and that your Arduino board is connected to your desktop and eagerly awaiting a new sketch.

{% highlight cpp linenos %}
/*
 Simple LED sketch
 */

int led = 13; // Pin 13

void setup()
{
    pinMode(led, OUTPUT); // Set pin 13 as digital out

    // Turn on the LED
    digitalWrite(led, HIGH);
}

void loop()
{
    // Loop infinitely, let's not worry about this for now
}
{% endhighlight %}

Now run **“Verify”** and then **“Upload”**. You should see the LED lighted as well as an orange LED on the board itself (if you have an UNO). Well that was very easy. Now we will make use of the **Serial** standard library to enable and disable the LED using the **serial port**.

If you are not yet sure what methods of serial communication your board has available, I suggest you take a look at the [Arduino – Serial](http://arduino.cc/en/reference/serial) section for more information.

{% highlight cpp linenos %}
/*
 Simple LED sketch
 */

int led = 13; // Pin 13

void setup()
{
    pinMode(led, OUTPUT); // Set pin 13 as digital out

    // Start up serial connection
    Serial.begin(9600); // baud rate
    Serial.flush();
}

void loop()
{
    String input = "";

    // Read any serial input
    while (Serial.available() > 0)
    {
        input += (char) Serial.read(); // Read in one char at a time
        delay(5); // Delay for 5 ms so the next char has time to be received
    }

    if (input == "on")
    {
        digitalWrite(led, HIGH); // on
    }
    else if (input == "off")
    {
        digitalWrite(led, LOW); // off
    }
}
{% endhighlight %}

**“Verify”** and **“Upload”** your sketch. Once it’s done uploading, open up the IDE **Serial Monitor** by going to **Tools – Serial Monitor**. You should be presented with an input field and a text area. The textarea will be populated by `Serial.println("text out");` calls (we have none, so it will be blank) and the input field is your `Serial.read()` call.

Type the word **“on”** in the input field (without the quotes) and press enter. Your LED should now turn on. Type **“off”** and it should be powered off. :)

You will note that we used a **5 millisecond** delay while reading the serial input. Some might argue that any type of delay is a bad idea, but we specifically need this because of the delay between received serial data. The Arduino might experience a few milliseconds delay between each serial character it receives and if we remove the delay the `Serial.available()` call will return false after each character it reads, resulting in undesired inputs being populated into the `input` variable.

So, assuming your LED did light up…our job is done (for now). If you had any problems or something that didn’t work, feel free to post about it in the comments and I will try to help. Next time we will have a look at some more complicated Arduino examples. Here is a video of my first attempt at running a remote control car from my desktop…well, atleast the steering works ;)

<div class="videowrapper">
    <iframe src="//www.youtube.com/embed/LqzbRwjU4dE?html5=1" allowfullscreen="" frameborder="0" height="315" width="560"></iframe>
</div>