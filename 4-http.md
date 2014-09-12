[<< previous](3-error-handler.md)

### HTTP

PHP already has a few things built in to make working with HTTP easier. For example there are the [superglobals](http://php.net/manual/en/language.variables.superglobals.php) that contain the request information.

These are good if you just want to get a quick and dirty script runnin. But if you want to write clean, maintanable, [SOLID](http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29) code, then you will want a class with a nice object oriented interface that you can use in your application.

Once again, you don't have to reinvent the wheel and just install a package. I decided to write my own [HTTP component](https://github.com/PatrickLouys/http).

Some alternatives: [Symfony HttpFoundation](https://github.com/symfony/HttpFoundation), [Nette HTTP Component](https://github.com/nette/http), [Aura Http](https://github.com/auraphp/Aura.Http), [sabre/http](https://github.com/fruux/sabre-http)

In this tutorial I will use my own HTTP component, but of course you can use whichever package you like most. Just change the code accordingly.

Again, edit the `composer.json` to add the new component and then run `composer update`:

```
"require": {
    "php": ">=5.5.0",
    "filp/whoops": ">=1.1.2",
    "patricklouys/http": ">=1.0.2"
},
```


to be continued...