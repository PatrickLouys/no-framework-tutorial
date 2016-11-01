[<< previous](05-router.md) | [next >>](07-inversion-of-control.md)

### Dispatching to a Class

In this tutorial we won't implement [MVC (Model-View-Controller)](http://martinfowler.com/eaaCatalog/modelViewController.html). MVC can't be implemented properly in PHP anyway, at least not in the way it was originally conceived. If you want to learn more about this, read [A Beginner's Guide To MVC](http://blog.ircmaxell.com/2014/11/a-beginners-guide-to-mvc-for-web.html) and the followup posts.

So forget about MVC and instead let's worry about [separation of concerns](http://en.wikipedia.org/wiki/Separation_of_concerns).

We will need a descriptive name for the classes that handle the requests. For this tutorial I will use `Controllers` because that will be familiar for the people coming from a framework background. You could also name them `Handlers`.

Create a new folder inside the `src/` folder with the name `Controllers`.In this folder we will place all our controller classes. In there, create a `Homepage.php` file.

```php
<?php declare(strict_types = 1);

namespace Example\Controllers;

class Homepage
{
    public function show()
    {
        echo 'Hello World';
    }
}
```

The autoloader will only work if the namespace of a class matches the file path and the file name equals the class name. At the beginning I defined `Example` as the root namespace of the application so this is referring to the `src/` folder.

Now let's change the hello world route so that it calls your new class method instead of the closure. Change your `Routes.php` to this:

```php
return [
    ['GET', '/', ['Example\Controllers\Homepage', 'show']],
];
```

Instead of just a callable you are now passing an array. The first value is the fully namespaced classname, the second one the method name that you want to call.

To make this work, you will also have to do a small refactor to the routing part of the `Bootstrap.php`:

```php
case \FastRoute\Dispatcher::FOUND:
    $className = $routeInfo[1][0];
    $method = $routeInfo[1][1];
    $vars = $routeInfo[2];
    
    $class = new $className;
    $class->$method($vars);
    break;
```

So instead of just calling a method you are now instantiating an object and then calling the method on it.

Now if you visit `http://localhost:8000/` everything should work. If not, go back and debug. And of course don't forget to commit your changes.

[<< previous](05-router.md) | [next >>](07-inversion-of-control.md)
