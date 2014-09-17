[<< previous](7-inversion-of-control.md)

### Dependency Injector

A dependency injector resolves the dependencies of your class and makes sure that the correct objects are injected when the class is instantiated.

My favorite injector is [Auryn](https://github.com/rdlowrey/Auryn), so install `rdlowrey/auryn` with composer or use one of the alternatives below:
[Pimple](http://pimple.sensiolabs.org/), [Orno DI](https://github.com/orno/di), [PHP-DI](https://github.com/mnapoli/PHP-DI)

Create a new file called `Dependencies.php` in your `src/` folder. In there add the following content:

```php
<?php

$injector = new \Auryn\Provider;

$injector->share('Http\CookieBuilder');
$injector->delegate('Http\CookieBuilder', function($environment){
    $cookieBuilder = new \Http\CookieBuilder;
    $cookieBuilder->setDefaultSecure($environment === 'production');
    return $cookieBuilder;
});

$injector->alias('Http\Response', 'Http\HttpResponse');
$injector->share('Http\HttpRequest');
$injector->define('Http\HttpRequest', [
    ':get' => $_GET,
    ':post' => $_POST,
    ':cookies' => $_COOKIE,
    ':files' => $_FILES,
    ':server' => $_SERVER,
]);

$injector->alias('Http\Request', 'Http\HttpRequest');
$injector->share('Http\HttpResponse');

return $injector;
```

Make sure you understand what `alias`, `share` and `define` are doing before you continue. You can read about them in the [Auryn documentation](https://github.com/rdlowrey/Auryn).

You are sharing the HTTP objects because there would not be much point in adding content to one object and then returning another one. So by sharing it you always get the same instance.

The alias allows you to type hint the interface instead of the class name. This makes it easy to switch the implementation without having to go back and edit all your classes that use the old implementation.

Of course your `Bootstrap.php` will also need to be changed. Before you were setting up `$request` and `$response` with `new` calls. Switch that to the injector now so that we are using the same instance of those objects everywhere.

```php
$injector = include('Dependencies.php');

$request = $injector->make('Http\HttpRequest');
$response = $injector->make('Http\HttpResponse');
```

The other part that has to be changed is the dispatching of the route. Before you had the following code:

```php
$class = new $className($response);
$class->$method($vars);
```

Change that to the following:

```php
$class = $injector->make($className);
$class->$method($vars);
```

Now all your controller constructor dependencies will be automatically resolved with Auryn.

to be continued...
