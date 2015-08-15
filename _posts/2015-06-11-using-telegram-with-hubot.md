---
layout: post
title: Using Telegram With Hubot
redirect_from:
    /2015/06/11/using-telegram-with-hubot.html
---

[Telegram](https://www.telegram.org/){:target="_blank"} recently released a new [Bot API](https://core.telegram.org/bots/api){:target="_blank"}, which makes it relatively easy to integrate with applications like [GitHub Hubot](https://hubot.github.com/){:target="_blank"}.

I recently collaborated on an adapter for [Hubot](https://hubot.github.com/){:target="_blank"} that makes the integration trivial.

## Register A Bot With BotFather

First thing you need is a [Telegram Bot](https://core.telegram.org/bots){:target="_blank"}, which you can easily register using their [BotFather](https://core.telegram.org/bots#botfather){:target="_blank"} application. You need to add the [BotFather](https://telegram.me/botfather){:target="_blank"} to your Telegram application and create a new bot by typing **/newbot** and following the instructions, this will result in a **token** that you will use later when executing your bot.

## Configure The Hubot Instance

In order for [Hubot](https://hubot.github.com/docs/){:target="_blank"} to work, you will need to install [NodeJS](http://nodejs.org/){:target="_blank"} and [NPM](https://www.npmjs.com/){:target="_blank"}. When you have installed those dependencies, you can set up a new default [Hubot](https://hubot.github.com/docs/){:target="_blank"} by running the following commands:

{% highlight bash %}
npm install -g yo generator-hubot
mkdir myhubot
cd myhubot
yo hubot --defaults
{% endhighlight %}

You can test your [Hubot](https://hubot.github.com/docs/){:target="_blank"} by typing `bin/hubot` which will launch a CLI version of Hubot for testing. You now need to install the [Telegram](https://www.telegram.org/){:target="_blank"} dependency, you can do so by running:

{% highlight bash %}
npm install --save hubot-telegram
{% endhighlight %}

This will download the source code and add it to your `package.json` file in the Hubot directory.

**Note:** If Hubot is not available under `bin/hubot` you can try and run it from the Hubot folder with `./node_modules/.bin/hubot`.

## Running Hubot

Now that you have everything configured and installed, you can start your [Hubot](https://hubot.github.com/docs/){:target="_blank"} instance with the following command:

{% highlight bash %}
TELEGRAM_TOKEN=<token> bin/hubot -a telegram -n botname
{% endhighlight %}

If everything has been configured correctly, the bot should start up and announce itself based on the name you gave it when you created it on [Telegram](https://www.telegram.org/){:target="_blank"}. To get started type **@botname help** in a chat with your bot, this will produce a list of available Hubot commands. You can add more commands to Hubot by following some of the instructions on [their site](https://hubot.github.com/docs/#scripts){:target="_blank"}.

**Note:** The **botname** you specify should be the same as your Telegram bot name for a consistent and predictable experience.