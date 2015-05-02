---
layout: post
title: Design Pattern - PHP Event Dispatcher
fullview: true
---

Almost every time the topic of events in PHP comes up, the responses I usually get tend to be in the direction of “PHP doesn’t have real events”, “PHP executes synchronously, how can it use events” etc. Although there are some truth to these statements, events doesn’t have to by asynchronous or non-blocking. Events are defined as **“something that happens”**. So if you are uncomfortable with calling them events, just call them **“hooks”**.

It’s quite easy to create the behaviour of events in PHP. There are some design patterns like the [Observer](http://www.php.net/manual/en/class.splobserver.php) pattern, which is as close as you need to get to events. The concept behind an [Observer](http://www.php.net/manual/en/class.splobserver.php) pattern is that you have a **subject (publisher)** that notifies **subscribers** when something occurs. The publisher has no idea what the subscriber is going to do with the data, all it knows is that the subscriber is sitting there waiting for a notification of when the specific scenario occurs.

{% highlight php linenos %}
<?php

// Interface for subscribers to listen for events
interface SubscriberInterface
{
    public function update(PublisherInterface $pub);
}

// Publishers are able to notify subscribers of events
interface PublisherInterface
{
    public function notify();
}

// Abstraction for a class that needs to implements the Observer pattern
abstract AbstractPublisher implements PublisherInterface
{
    private $observers;

    public function notify()
    {
        foreach ($this->observers as $obs)
        {
            $obs->update($this);
        }
    }

    public function addSubscriber(SubscriberInterface $sub)
    {
        $this->observers[] = $sub;
    }
}
{% endhighlight %}

This would be the typical **interface** and **abstraction** of what a publisher will look like. Very simple, it only needs to be able notify subscribers.

Let’s have a look at how the code sample below will notify it’s subscribers of when a blog is updated.

{% highlight php linenos %}
<?php

class BlogLogger implements SubscriberInterface
{
    public function update(BlogPublisher $pub)
    {
        // Log to file
        // $pub->getTitle();
    }
}

class BlogPublisher implements PublisherInterface
{
    public function getTitle()
    {
        return 'Blog Title';
    }

    public function updateBlog()
    {
        $this->notify();
    }
}
{% endhighlight %}

The example above shows us just how the subscriber and publisher interact with each other. We can take this even further, we can take this pattern and manipulate it a little to accomplish some powerful event management.

In the observer pattern, the subject (publisher) is responsible for keeping track of it’s own subscribers. For small systems or specific use cases, this will work just fine…but when you have a large system, it gets easier to take the **“management”** part of the events and put it into it’s own class or component.

By doing this, the framework or systems will know that they need to notify the **event manager** of when they do something important and the manager will be aware of any subscribers (if any).

{% highlight php linenos %}
<?php

class EventManager
{
    private $listeners = array();

    public function listen($event, $callback)
    {
        $this->listeners[$event][] = $callback;
    }

    public function dispatch($event, PublisherInterface $param)
    {
        foreach ($this->listeners[$event] as $listener)
        {
            call_user_func_array($listener, array($param));
        }
    }
}

class BlogPublisher implements PublisherInterface
{
    private $event;

    public function __construct(EventManager $event)
    {
        $this->event = $event;
    }

    public function getTitle()
    {
        return 'Blog Title';
    }

    public function getDispatcher()
    {
        return $this->event;
    }

    public function updateBlog()
    {
        $this->getDispatcher()->dispatch('blog_update', $this);
    }
}

// Create the manager
$event = new EventManager();

// Create the blog updater
$blog = new BlogPublisher($event);

// Register a subscriber, can be anything
$event->listen('blog_update', function(BlogPublisher $pub) {
    // Write to log
    // $pub->getTitle();
});

// Update the blog
$blog->updateBlog();
{% endhighlight %}

By abstracting the event management into a component of it’s own, we can introduce error handling into that component to ensure that any errors that might occur from events won’t interrupt the main flow of the program. In addition, we can have different types of exceptions where one of them might actually be capable of interrupting the main flow if necessary (like an ACL component that needs to blog certain actions).

You will also notice, the need for a `SubscriberInterface` is now no longer necessary. Using a manager means that we can assign any **“callable”** as a listener to an event. With this implementation, your subscribers can **“listen”** to certain events in the manager. These events don’t have to exist. This allows for the development of loosely coupled systems that can easily be replaced or removed with minimal affect.

However, you might have scenario’s where you want a certain class to listen to multiple events. If we change the `SubscriberInterface` to expose a list of events it wishes to subscribe to...it centralizes the location in which a subscriber can define it’s interest. If we don’t do this, it will be necessary to register multiple listeners and manually keep track of where they are registered. With the subscriber listening to multiple events, one `update` function won’t do. Instead of creating elaborate if statements or switch cases in the update function, it’s more ideal to have each event or notification map to a specified function.

{% highlight php linenos %}
<?php

interface SubscriberInterface
{
    public static function getSubscribedEvents();
}

class BlogSubscriber implements SubscriberInterface
{
    public static function getSubscribedEvents()
    {
        return array(
            'blog_update' => 'Listener'
        );
    }

    // The subscribed function
    public function Listener(BlogPublisher $pub)
    {
        // Write to log
        // $pub->getTitle();
    }
}

// We also need to extend the event manager to
// deal with subscribed interfaces.
class EventManager
{
    private $listeners = array();

    public function listen($event, $callback)
    {
        $this->listeners[$event][] = $callback;
    }

    public function dispatch($event, PublisherInterface $param)
    {
        foreach ($this->listeners[$event] as $listener)
        {
            call_user_func_array($listener, array($param));
        }
    }

    public function addSubscriber(SubscriberInterface $sub)
    {
        $listeners = $sub->getSubscribedEvents();

        foreach ($listeners as $event => $listener)
        {
            // Add the subscribed function as an event
            $this->listen($event, array($sub, $listener));
        }
    }
}

// How it works
$event = new EventManager();

// Create the blog updater
$blog = new BlogPublisher($event);

// Register a subscriber, can be anything
$subscriber = new BlogSubscriber();

// Add the subscriber
$event->addSubscriber($subscriber);

// Update the blog
$blog->updateBlog();
{% endhighlight %}

As you can see, it’s not that difficult to have PHP implement an event based design pattern. Just because these events execute synchronously does not mean we cannot assign priorities to them, or even stop propagation. We also get the benefit of being able to override certain behaviour of the system in these events. The makes building a plugin system or extension manager much easier.

One last addition we can make, is to publish certain event objects that inherit from a base event. By doing this, we can ensure that the subscribers always know exactly **“what”** is being published to them. Doing so will mean the publisher can change without affecting it’s subscribers.

{% highlight php linenos %}
<?php

abstract class Event { }

class BlogEvent extends Event
{
    private $title;

    public function __construct($title)
    {
        $this->title = $title;
    }

    public function getTitle()
    {
        return $this->title;
    }
}

// Now we need to change the manager and publisher to publish
// an event abstraction.
class EventManager
{
    ...
    // Pass the event as parameter
    public function dispatch($event, Event $event)
    {
        foreach ($this->listeners[$event] as $listener)
        {
            call_user_func_array($listener, array($event));
        }
    }
}


class BlogPublisher implements PublisherInterface
{
    ...

    public function updateBlog()
    {
        $event = new BlogEvent($this->getTitle());
        // Include the event when published
        $this->getDispatcher()->dispatch('blog_update', $event);
    }
}

// Our subscriber now changes to use the event object instead
class BlogSubscriber implements SubscriberInterface
{
    ...
    // The subscribed function
    public function Listener(BlogEvent $blog_event)
    {
        // Write to log
        // $blog_event->getTitle();
    }
}
{% endhighlight %}

Now our subscriber is aware of exactly how it can interact with the published result.

There are quite a few open source event libraries out there which this article is based on. I encourage you to use one of these libraries instead of writing your own, but I hope this article gave you insight into how events can be handled in PHP. For more elaborate examples of what can be done with an event dispatcher, I would recommend you take a look at the [Symfony Event Dispatcher](http://symfony.com/doc/current/components/event_dispatcher/introduction.html) component.