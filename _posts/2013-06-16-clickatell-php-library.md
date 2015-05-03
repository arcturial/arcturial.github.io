---
layout: post
title: Clickatell PHP Library
description: I created this PHP library to make it easier to integrate and easily switch protocols (types of API’s). Using this library you can easily go from using the HTTP API to using the XML API (or other) with almost no code modification required.
old_url: {{ site.url }}{{ post.url }}
---

[Clickatell](http://www.clickatell.com) offers quite a few API’s to help developers integrate with their SMS gateway. The only problem I found, was that the different protocols tend to behave differently.

I created this PHP library to make it easier to integrate and easily switch protocols (types of API’s). Using this library you can easily go from using the HTTP API to using the XML API (or other) with almost no code modification required.

### 1. Installation

This library uses [composer](http://www.getcomposer.org/) and can be acquired using the following in your `composer.json` file.

{% highlight php linenos %}
<?php
{
    "require": {
        "arcturial/clickatell": "*"
    }
}
{% endhighlight %}

### 2. Usage

The Clickatell library allows you specify several ways to connect to Clickatell. The current ones supported are HTTP and XML. These connections are called ‘Transports’. The default transport is **HTTP**.

{% highlight php linenos %}
<?php
$clickatell = new Clickatell($username, $password, $apiID);
$clickatell->sendMessage(array(1111111111), 'My Message');
{% endhighlight %}

You can specify a different output using the Clickatell constructor or using the `setTransport()` method.

{% highlight php linenos %}
<?php
$clickatell = new Clickatell(
    $username,
    password,
    $apiID,
    Clickatell::TRANSPORT_XML
);

// OR
$clickatell = new Clickatell($username, $password, $apiID);
$clickatell->setTransport(new ClickatellComponentTransportTransportXml);
{% endhighlight %}

NOTE: The library uses name spaces, and the Clickatell messenger is located at ClickatellClickatell

### 3. Supported API calls

Clickatell has a couple of different API’s that each support a subset of functions. We are going to refer to them as Messaging and Bulk Messaging API’s for this document.

#### Messaging API’s

The following are all messaging API’s.

`ClickatellComponentTransportTransportHttp`

`ClickatellComponentTransportTransportSoap`

`ClickatellComponentTransportTransportXml`

`ClickatellComponentTransportTransportSmtp`

These Transports all support the following functions

{% highlight php linenos %}
<?php
sendMessage(array $to, string $message, $from = "", $callback = true);
getBalance();
queryMessage($apiMsgId);
routeCoverage($msisdn);
getMessageCharge($apiMsgId);
{% endhighlight %}

#### Bulk Messaging API’s

The following are bulk messaging API’s. The have only a limited number of functions and are more suited for bulk messaging. Since they aren’t processed in real time, these Transports do not return the same results as the normal messaging API’s.

`ClickatellComponentTransportTransportSMTP`

These Transports all support the following functions

{% highlight php linenos %}
<?php
sendMessage(array $to, string $message, $from = "", $callback = true);
{% endhighlight %}

### 4. Events

This library provides a couple of events to extend the ability of the API’s. Current support events are `request` and `response`.

Example:

{% highlight php linenos %}
<?php
use ClickatellClickatell;

$clickatell = new Clickatell('[username]', '[password]', [api_id], Clickatell::HTTP_API);

$clickatell->on('request', function($data) {
    // $data = The parameters passed to the request
    print_r($data);
});

$clickatell->on('response', function($data) {
    // $data = The result of the API call.

    // This hook can be used to register multiple
    // listeners that can log to file/db or call another
    // service.
    print_r($data);
});
{% endhighlight %}