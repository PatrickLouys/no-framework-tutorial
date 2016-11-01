[<< previous](06-dispatching-to-a-class.md) | [next >>](08-dependency-injector.md)

### Inversion of Control

In the last part you have set up a controller class and generated output with `echo`. But let's not forget that we have a nice object oriented HTTP abstraction available. But right now it's not accessible inside your class.

The sane option is to use [inversion of control](http://en.wikipedia.org/wiki/Inversion_of_control). This means that instead of giving the class the responsiblity of creating the object it needs, you just ask for them. This is done with [dependency injection](http://en.wikipedia.org/wiki/Dependency_injection).

If this sounds a little complicated right now, don't worry. Just follow the tutorial and once you see how it is implemented, it will make sense.

Change your `Homepage` controller to the following:

```php
<?php declare(strict_types = 1);

namespace Example\Controllers;

use Http\Response;

class Homepage
{
    private $response;

    public function __construct(Response $response)
    {
        $this->response = $response;
    }

    public function show()
    {
        $this->response->setContent('Hello World');
    }
}
```

Note that we are [importing](http://php.net/manual/en/language.namespaces.importing.php) `Http\Response` at the top of the file. This means that whenever you use `Response` inside this file, it will resolve to the fully qualified name.

In the constructor we are now explicitly asking for a `Http\Response`. In this case, `Http\Response` is an interface. So any class that implements the interface can be injected. See [type hinting](http://php.net/manual/en/language.oop5.typehinting.php) and [interfaces](http://php.net/manual/en/language.oop5.interfaces.php) for reference.

Now the code will result in an error because we are not actually injecting anything. So let's fix that in the `Bootstrap.php` where we dispatch when a route was found:

```php
$class = new $className($response);
$class->$method($vars);
```

The `Http\HttpResponse` object implements the `Http\Response` interface, so it fulfills the contract and can be used.

Now everything should work again. But if you follow this example, all your objects that are instantiated this way will have the same objects injected. This is of course not good, so let's fix that in the next part.

[<< previous](06-dispatching-to-a-class.md) | [next >>](08-dependency-injector.md)
