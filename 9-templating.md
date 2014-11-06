[<< previous](8-dependency-injector.md)

### Templating

A template engine is not necessary with PHP because the language itself can take care of that. But it can make things like escaping values easier. They also make it easier to draw a clear line between your application logic and the template files which should only put your variables into the HTML code.

A good quick read on this is [ircmaxell on templating](http://blog.ircmaxell.com/2012/12/on-templating.html). Please also read [this](http://chadminick.com/articles/simple-php-template-engine.html) for a different opinion on the topic. Personally I don't have a strong opinion on the topic, so decide yourself which approach works better for you.

For this tutorial we will use a PHP implementation of [Mustache](https://github.com/bobthecow/mustache.php). So install that package before you continue.

Other well known alternatives would be [Twig](http://twig.sensiolabs.org/) or [Smarty](http://www.smarty.net/), but they are both pretty bloated and offer too much functionality for just a template engine.

Now please go and have a look at the source code of the [engine class](https://github.com/bobthecow/mustache.php/blob/master/src/Mustache/Engine.php). As you can see, the class does not implement an interface.

You could just type hint against the concrete class. But the problem with this approach is that you create tight coupling.

In other words, all your code that uses the engine will be coupled to this mustache package. If you want to change the implementation you have a problem. Maybe you want to switch to Twig, maybe you want to write your own class or you want to add functionality to the engine. You can't do that without going back and changing all your code that is tightly coupled.

What we want is loose coupling. We will type hint against an interface that implements an interface. So if you need another implementation, you just implement that interface in your new class and inject the new class instead. 

Instead of editing the code of the package we will use the [adapter pattern](http://en.wikipedia.org/wiki/Adapter_pattern). This sounds a lot more complicated than it is, so just follow along.

First let's define the interface that we want. Remember the [interface segregation principle](http://en.wikipedia.org/wiki/Interface_segregation_principle). This means that instead of large interfaces with a lot of methods we want to make each interface as small as possible. A class can extend multiple interfaces if necessary.

So what does our template engine actually need to do? For now we really just need a simple `render` method. Create a new folder in your `src/` folder with the name `Template` where you can put all the template related things.

In there create a new interface `Renderable.php` that looks like this:

```php
<?php

namespace Example\Template;

interface Renderable
{
    public function render($template, $data = []);
}
```

[<< previous](8-dependency-injector.md)