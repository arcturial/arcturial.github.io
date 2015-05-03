---
layout: post
title: Testing Javascript With Mock Objects And Spies
description: In this article, I provide some insight about how I wrote the Mock.js, a lightweight Javascript mocking library, and how to go about using it in a test environment.
---

One of the biggest problem we are confronted with when writing tests, is the inability to isolate certain pieces of code. This makes the tests less valuable, as we don’t know which piece of code might be causing a test to fail.

This problem has been solved by introducing mock objects into the code. There are various test frameworks and mock objects available for some of the more popular languages. Javascript on the other hand, does have a couple of good ones available ([Sinon.JS](http://sinonjs.org/)), but the selection is not that large.

When I first started browsing around for Javascript testing frameworks, mock objects were high on my list of requirements. I tested a couple of them and then decided I want to create my own. One of my reasons were to familiarize myself with how these libraries utilize Javascript and prototypes to “fake” or “track” certain method calls.

In this article, I provide some insight about how I wrote the [Mock.js](https://github.com/arcturial/mock.js) (a lightweight Javascript mocking library) and how to go about using it in a test environment.

### Getting Started

First off, we have to include the mock library in our code.

{% highlight html linenos %}
<html>
    <head>
    <script type="text/javascript" src="mock.js"></script>
    </head>
{% endhighlight %}


The objective of this test is to log the result of an object that writes to disk. We want to test the ability of the `Logger` object to be able to accept a `FileSystem` result object and log it “somewhere”.

The problem with this test is, that the test halts until it’s done writing to file. It also executes the `FileSystem` object. Which means if the `FileSystem` fails our test for testing the `Logger` also fails.

One way we can solve this problem is by **“faking”** the `write` function and stopping it from attempting to write to disk.

We know what the result of a successful `write` call should be. So is there not a way we can just tell the logger what it’s about to receive as input?

{% highlight html linenos %}
<body>
    <div id="qunit"></div>
    <div id="qunit-fixture"></div>

    <script>
        test("test write to file", function() {
            var filesys = new FileSystem();

            var result = filesys.write('file.txt', 'content');
            var logResult = Logger.log(result);

            ok(logResult , 'done logging');
        }
    </script>
</body>
{% endhighlight %}

Using a `Mock.Spy`, we can tell the `FileSystem` object to stop it’s execution and return a specific result when the mocked function is called (in this case, the `write` function). Using `consume()`, we tell the `FileSystem` to always return **true** when the `write()` function is called.

Now the test is only testing the `Logger` functionality and **“fakes”** the `FileSystem`

**Mission accomplished!** We now have a nice atomic test.

The `Mock.Spy` also has the ability to check if a certain object has been called using the `called()` method. Another nifty function is the ability to see what parameters were used to call the function using `calledWith()`

### How It Works

Now that you have seen the “Mock” in action. We can analyze it a little and see exactly how it works.

Some libraries opt for using the Javascript object prototype to change the way a function behaves. The problem I found with this is that you must always **“reset”** these mocks to their original state. If you don’t, the next time the object executes in a complete different test…it might still be affected by the mock.

The way I created [Mock.js](https://github.com/arcturial/mock.js) was to override the current instance of an object and replace it’s “mocked” methods with a “tracker” that changes the behavior of a method or tracks when a method is executed.

{% highlight javascript linenos %}
var context = this;
var obj = [object to mock];

// Keep track of the current method functionality
var functionCont = obj[method];

// Now we override the method and attach a tracket
obj[method] = function()
{
    // Register the action on the tracker
    context.track.callCount++;

    // Invoke original method
    context.functionCont.apply(obj, arguments);
);
{% endhighlight %}

What this piece of code does, is it takes a current object instance and stored a certain methods functionality in a new variable. The method is then overwritten with a new function that increments our call counter. Then, we execute the original method (this can be changed if an object has been consumed).

When an object is consumed, instead of calling the original functionality. The value of the consumption is returned instead. So, if we call `spy.consume('return true');` then the function will return a string **“return true”** instead of the original function being invoked.

### Conclusion

[Mock.js](https://github.com/arcturial/mock.js) is a very lightweight mock library that covers some basic mocking functionality. I tend to use it in most of my projects because it actually has everything I need. It’s also fairly easy to mock any object including global `window` objects.

If you want to extend this library or take a stab at writing your own. Please feel free to have a look at the code for `Mock.js` on [Github](https://github.com/arcturial/mock.js).

**Note:** when mocking some global objects such as `jQuery.ajax`, the mock needs to be released using `spy.release()`. Since the object has a global reference, it functions almost like a singleton. If the mock is not released, all ajax calls can be affected.