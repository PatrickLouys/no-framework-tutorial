[<< previous](8-dependency-injector.md)

### Templating

A template engine is not necessary with PHP because the language itself can take care of that. But it can make things like escaping values easier. They also make it easier to draw a clear line between your application logic and the template files which should only put your variables into the HTML code.

A good quick read on this is [ircmaxell on templating](http://blog.ircmaxell.com/2012/12/on-templating.html). Please also read [this](http://chadminick.com/articles/simple-php-template-engine.html) for a different opinion on the topic. Personally I don't have a strong opinion on the topic, so decide yourself which approach works better for you.

For this tutorial we will use a PHP implementation of [Mustache](https://github.com/bobthecow/mustache.php).

Other well known alternatives would be [Twig](http://twig.sensiolabs.org/) or [Smarty](http://www.smarty.net/), but they are both pretty bloated and offer too much functionality for just a template engine.

to be continued...

[<< previous](8-dependency-injector.md)