---
layout: post
title: Callback Support For Clickatell PHP Library
old_url: http://www.cainsvault.com/callback-support-clickatell-php-library/
redirect_from:
    /2014/01/01/callback-clickatell-php.html
---

I recently updated the [Clickatell PHP Library](https://github.com/arcturial/clickatell) with added support for MT callbacks (tag 1.0.1). In order to utilize it, you can register a closure callback with the Clickatell library using `Clickatell::parseCallback();`.

{% highlight php linenos %}
<?php

use Clickatell\Clickatell;

Clickatell::parseCallback(function ($values) {

    // Executes if it finds the required parameters in $_GET
    // apiMsgId, cliMsgId, to, timestampe, from, status, charge
    var_dump($values);

});
{% endhighlight %}

Future versions will hopefully include MO callback support.