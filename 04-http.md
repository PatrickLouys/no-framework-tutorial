[<< previous](03-error-handler.md) | [next >>](05-router.md)

### HTTP

PHP already has a few things built in to make working with HTTP easier. For example there are the [superglobals](http://php.net/manual/en/language.variables.superglobals.php) that contain the request information.

These are good if you just want to get a small script up and running, something that won't be hard to maintain. However, if you want to write clean, maintainable, [SOLID](http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29) code, then you will want a class with a nice object-oriented interface that you can use in your application instead.

Once again, you don't have to reinvent the wheel and just install a package. I decided to write my own [HTTP component](https://github.com/PatrickLouys/http) because I did not like the existing components, but you don't have to do the same.

Some alternatives: [Symfony HttpFoundation](https://github.com/symfony/HttpFoundation), [Nette HTTP Component](https://github.com/nette/http), [Aura Web](https://github.com/auraphp/Aura.Web), [sabre/http](https://github.com/fruux/sabre-http)

In this tutorial I will use my own HTTP component, but of course you can use any package that you like. You just have to adapt the code from the tutorial yourself.

Again, edit the `composer.json` to add the new component and then run `composer update`:

```json
  "require": {
    "php": ">=7.0.0",
    "filp/whoops": "~2.1",
    "patricklouys/http": "~1.4"
  },
```

Now you can add the following below your error handler code in your `Bootstrap.php` (and don't forget to remove the exception):

```php
$request = new \Http\HttpRequest($_GET, $_POST, $_COOKIE, $_FILES, $_SERVER);
$response = new \Http\HttpResponse;
```

This sets up the `Request` and `Response` objects that you can use in your other classes to get request data and send a response back to the browser.

To actually send something back, you will also need to add the following snippet at the end of your `Bootstrap.php` file:

```php
foreach ($response->getHeaders() as $header) {
    header($header, false);
}

echo $response->getContent();
```

This will send the response data to the browser. If you don't do this, nothing happens as the `Response` object only stores data. This is handled differently by most other HTTP components where the classes send data back to the browser as a side-effect, so keep that in mind if you use another component.

The second parameter of `header()` is false because otherwise existing headers will be overwritten.

Right now it is just sending an empty response back to the browser with the status code `200`; to change that, add the following code between the code snippets from above (just on top of the `foreach` statement):

```php
$content = '<h1>Hello World</h1>';
$response->setContent($content);
```

If you want to try a 404 error, use the following code:

```php
$response->setContent('404 - Page not found');
$response->setStatusCode(404);
```

Remember that the object is only storing data, so if you set multiple status codes before you send the response, only the last one will be applied.

I will show you in later parts how to use the different features of the components. In the meantime, feel free to read the [documentation](https://github.com/PatrickLouys/http) or the source code if you want to find out how something works.

[<< previous](03-error-handler.md) | [next >>](05-router.md)
