[<< previous](5-router.md)

### Controllers

When I talk about a controller in this tutorial then I am just referring to a class that has handler methods. I am not talking about [MVC (Model-View-Controller)](http://martinfowler.com/eaaCatalog/modelViewController.html) controllers. MVC can't be implemented properly in PHP anyway, at least not in the way it was originally conceived. So forget about MVC and instead let's worry about [separation of concerns](http://en.wikipedia.org/wiki/Separation_of_concerns).

Create a new folder inside the `src/` folder with the name `HelloWorld`. This will be where all your hello world related code will end up in. In there, create `HelloWorldController.php`.

```
<?php

namespace Example\HelloWorld;

class HelloWorldController
{
    public function helloWorld()
    {
        echo 'Hello World';
    }
}
```

The autoloader will only work if the namespace of a class matches the file path and the file name equals the class name. At the beginning I defined `Example` as the root namespace of the application so this is referring to the `src/` folder.

Now let's change the hello world route so that it calls your new controller method instead of the closure. Change your `Routes.php` to this:

```
return [
    ['GET', '/hello-world', [
        'Example\HelloWorld\HelloWorldController', 
        'helloWorld',
    ]],
];
```

Instead of just a callable you are now passing an array. The first value is the fully namespaced controller classname, the second one the method name that you want to call.

To make this work, you will also have to do a small refactor to the routing part of the `Bootstrap.php`:

```
case \FastRoute\Dispatcher::FOUND:
    $className = $routeInfo[1][0];
    $method = $routeInfo[1][1];
    $vars = $routeInfo[2];
    
    $class = new $className;
    $class->$method($vars);
    break;
```

So instead of just calling a handler method you are now instantiating the controller object and then calling the method on it.

Now if you visit `http://localhost:8000/hello-world` everything should work. If not, go back and debug. And of course don't forget to commit your changes.

to be continued...
