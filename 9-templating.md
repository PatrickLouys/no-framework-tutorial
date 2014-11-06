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

interface Engine
{
    public function render($template, $data = []);
}
```

Now that this is sorted out, let's create the mustache adapter class. In the same folder, create the file `MustacheEngineAdapter.php` with the following content:

```php
<?php

namespace Example\Template;

use Mustache_Engine;

class MustacheEngineAdapter implements Engine
{
    private $engine;

    public function __construct(Mustache_Engine $engine)
    {
        $this->engine = $engine;
    }

    public function render($template, $data = [])
    {
        return $this->engine->render($template, $data);
    }
}
```

As you can see the adapter is really simple. While the original class had a lot of methods, our adapter is really simple and only fulfills the interface.

Of course we also have to add a definition in our `Dependencies.php` file because otherwise the injector won't know which implementation he has to inject when you hint for the interface. Add this line:

`$injector->alias('Example\Template\Engine', 'Example\Template\MustacheEngineAdapter');`

Now in your `HelloWorldPresenter`, add the new dependency like this:

```php
<?php

namespace Example\HelloWorld;

use Http\Request;
use Http\Response;
use Example\Template\Engine as TemplateEngine;

class HelloWorldPresenter
{
    private $request;
    private $response;
    private $templateEngine;

    public function __construct(
        Request $request, 
        Response $response,
        TemplateEngine $templateEngine
    ) {
        $this->request = $request;
        $this->response = $response;
        $this->templateEngine = $templateEngine;
    }

...
```

As you can see I imported the engine with an alias. Without the full namespace it would be relatively unclear what a class does if it is just referenced by `Engine`. Also, another part of the application might also have a class with the name `Engine`. So to avoid that I give it a short and descriptive alias.

We also have to rewrite the `hello` method. Please note that while we are just passing in a simple array, Mustache also gives you the option to pass in a view context object. We will go over this later, for now let's keep it as simple as possible.

```php
    public function hello()
    {
        $data = [
            'name' => $this->request->getParameter('name', 'stranger'),
        ];
        $content = $this->templateEngine->render('Hello {{name}}', $data);
        $this->response->setContent($content);
    }
```

Now go check quickly in your browser if everything works. By default Mustache uses a simple string handler. But what we want is template files, so let's go back and change that.

To make this change we need to pass an options array to the `Mustache_Engine` constructor. So let's go back to the `Dependencies.php` file and add the following code:

```php
$injector->define('Mustache_Engine', [
    ':options' => [
        'loader' => new Mustache_Loader_FilesystemLoader(dirname(__DIR__).'/templates'),
    ],
]);
```

In your project root folder, create a `templates` folder. In there, create a folder `HelloWorld` and in there a file `Hello.mustache`. The content of the file should look like this:

```
<h1>Hello World</h1>
Hello {{ name }}
```

Now you can go back to your `HelloWorldPresenter` and change the render line to `$content = $this->templateEngine->render('HelloWorld/Hello', $data);`

Navigate to the hello page in your browser to make sure everything works. And as always, don't forget to commit your changes.

[<< previous](8-dependency-injector.md)