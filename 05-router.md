[<< previous](04-http.md) | [next >>](06-dispatching-to-a-class.md)

### Router

A router dispatches to different handlers depending on rules that you have set up.

With your current setup it does not matter what URL is used to access the application, it will always result in the same response. So let's fix that now.

I will use [FastRoute](https://github.com/nikic/FastRoute) in this tutorial. But as always, you can pick your own favorite package.

Alternative packages: [symfony/Routing](https://github.com/symfony/Routing), [Aura.Router](https://github.com/auraphp/Aura.Router), [fuelphp/routing](https://github.com/fuelphp/routing), [Klein](https://github.com/chriso/klein.php)

By now you know how to install Composer packages, so I will leave that to you.

Now add this code block to your `Bootstrap.php` file where you added the 'hello world' message in the last chapter.

```php
$dispatcher = \FastRoute\simpleDispatcher(function (\FastRoute\RouteCollector $r) {
    $r->addRoute('GET', '/hello-world', function () {
        echo 'Hello World';
    });
    $r->addRoute('GET', '/another-route', function () {
        echo 'This works too';
    });
});

$routeInfo = $dispatcher->dispatch($request->getMethod(), $request->getPath());
switch ($routeInfo[0]) {
    case \FastRoute\Dispatcher::NOT_FOUND:
        $response->setContent('404 - Page not found');
        $response->setStatusCode(404);
        break;
    case \FastRoute\Dispatcher::METHOD_NOT_ALLOWED:
        $response->setContent('405 - Method not allowed');
        $response->setStatusCode(405);
        break;
    case \FastRoute\Dispatcher::FOUND:
        $handler = $routeInfo[1];
        $vars = $routeInfo[2];
        call_user_func($handler, $vars);
        break;
}
```

In the first part of the code, you are registering the available routes for your application. In the second part, the dispatcher gets called and the appropriate part of the switch statement will be executed. If a route was found, the handler callable will be executed.

This setup might work for really small applications, but once you start adding a few routes your bootstrap file will quickly get cluttered. So let's move them out into a separate file.

Create a `Routes.php` file in the `src/` folder. It should look like this:

```php
<?php declare(strict_types = 1);

return [
    ['GET', '/hello-world', function () {
        echo 'Hello World';
    }],
    ['GET', '/another-route', function () {
        echo 'This works too';
    }],
];
```

Now let's rewrite the route dispatcher part to use the `Routes.php` file.

```php
$routeDefinitionCallback = function (\FastRoute\RouteCollector $r) {
    $routes = include('Routes.php');
    foreach ($routes as $route) {
        $r->addRoute($route[0], $route[1], $route[2]);
    }
};

$dispatcher = \FastRoute\simpleDispatcher($routeDefinitionCallback);
```

This is already an improvement, but now all the handler code is in the `Routes.php` file. This is not optimal, so let's fix that in the next part.

Don't forget to commit your changes at the end of each chapter.

[<< previous](04-http.md) | [next >>](06-dispatching-to-a-class.md)
