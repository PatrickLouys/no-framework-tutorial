[<< previous](6-controllers.md) | [next >>](8-dependency-injector.md)

### Inversion of Control

In the last part you have set up a controller and were able to generate output with `echo`. But let's not forget that you have a nice object oriented HTTP abstraction available. But you still need to make it accessible inside the controller class.

The sane option is to use [inversion of control](http://en.wikipedia.org/wiki/Inversion_of_control). This means that instead of giving the class the responsiblity of creating the object it needs, you just ask for them. This is done with [dependency injection](http://en.wikipedia.org/wiki/Dependency_injection).

If it sounds a little complicated right now, don't worry. Just follow the tutorial and once you see how it is implemented things will make more sense.

Change your `HelloWorldController` to the following:

```php
<?php

namespace Example\HelloWorld;

use Http\Response;

class HelloWorldController
{
    private $response;

    public function __construct(Response $response)
    {
        $this->response = $response;
    }

    public function hello()
    {
        $this->response->setContent('Hello World');
    }
}
```

Please note that you are [importing](http://php.net/manual/en/language.namespaces.importing.php) `Http\Response` at the top of the file. This means that whenever you use `Response` inside this file, it will resolve to the fully qualified name.

In the contructor you are now explicitly asking for a `Http\Response`. In this case, `Http\Response` is an interface. So any class that implements the interface can be injected. See [type hinting](http://php.net/manual/en/language.oop5.typehinting.php) and [interfaces](http://php.net/manual/en/language.oop5.interfaces.php) for reference.

Now the code will result in an error because you are not actually injecting anything. So let's fix that in your `Bootstrap.php` where you dispatch when a route was found:

```php
$class = new $className($response);
$class->$method($vars);
```

The `Http\HttpResponse` object implements the `Http\Response` interface, so it fulfills the contract and can be used.

Now everything should work again. But if you follow this example, all your controllers will have the same objects injected. This is of course not good, so let's fix that in the next part.

[<< previous](6-controllers.md) | [next >>](8-dependency-injector.md)