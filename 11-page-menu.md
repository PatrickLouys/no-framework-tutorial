[<< previous](10-dynamic-pages.md) | [next >>](12-frontend.md)

### Page Menu

Now we have made a few nice dynamic pages. But nobody can find them.

Let's fix that now. In this chapter we will create a menu with links to all of our pages.

When we have a menu, we will want to be able to reuse the same code on multiple pages. We could create a separate file and include it every time, but there is a better solution.

It is more practical to have templates that are able to extend other templates, like a layout for example. Then we can have all the layout related code in a single file and we don't have to include header and footer files in every template.

Our implementation of mustache does not support this. We could write code to work around this, which will take time and could introduce some bugs. Or we could switch to a library that already supports this and is well tested. [Twig](http://twig.sensiolabs.org/) for example.

Now you might wonder why we didn't start with Twig right away. Because this is a good example to show why using interfaces and writing loosely-coupled code is a good idea. Like in the real world, the requirements suddenly changed and now our code needs to adapt.

Remember how you created a `MustacheRenderer` in [chapter 9](09-templating.md)? This time, we create a `TwigRenderer` that implements the same interface.

But before we start, install the latest version of Twig with composer (`composer require "twig/twig:~1.0"`).

Then create the a `TwigRenderer.php` in your `src/Template` folder that looks like this:

```php 
<?php declare(strict_types = 1);

namespace Example\Template;

use Twig_Environment;

class TwigRenderer implements Renderer
{
    private $renderer;

    public function __construct(Twig_Environment $renderer)
    {
        $this->renderer = $renderer;
    }

    public function render($template, $data = []) : string
    {
        return $this->renderer->render("$template.html", $data);
    }
}
```

As you can see, on the render function call a `.html` is added. This is because Twig does not add a file ending by default and you would have to specifiy it on every call otherwise. By doing it like this, you can use it in the same way as you used Mustache.

Add the following code to your `Dependencies.php` file: 

```php
$injector->delegate('Twig_Environment', function () use ($injector) {
    $loader = new Twig_Loader_Filesystem(dirname(__DIR__) . '/templates');
    $twig = new Twig_Environment($loader);
    return $twig;
});
```

Instead of just defining the dependencies, we are using a delegate to give the responsibility to create the class to a function. This will be useful in the future.

Now you can switch the `Renderer` alias from `MustacheRenderer` to `TwigRenderer`. Now by default Twig will be used instead of Mustache.

If you have a look at the site in your browser, everything should work now as before. Now let's get started with the actual menu.

To start we will just send a hardcoded array to the template. Go to you `Homepage` controller and change your `$data` array to this:

```php
$data = [
    'name' => $this->request->getParameter('name', 'stranger'),
    'menuItems' => [['href' => '/', 'text' => 'Homepage']],
];
```

At the top of your `Homepage.html` file add this code:

```php
{% for item in menuItems %}
    <a href="{{ item.href }}">{{ item.text }}</a><br>
{% endfor %}
```

Now if you refresh the homepage in the browser, you should see a link.

The menu works on the homepage, but we want it on all our pages. We could copy it over to all the template files, but that would be a bad idea. Then if something changes, you would have to go change all the files.

So instead we are going to use a layout that can be used by all the templates.

Create a `Layout.html` in your `templates` folder with the following content:

```php
{% for item in menuItems %}
    <a href="{{ item['href'] }}">{{ item['text'] }}</a><br>
{% endfor %}
<hr>
{% block content %}
{% endblock %}
```

Then change your `Homepage.html` to this:

```php
{% extends "Layout.html" %}
{% block content %}
    <h1>Hello World</h1>
    Hello {{ name }}
{% endblock %}
```

And your `Page.html` to this:

```php
{% extends "Layout.html" %}
{% block content %}
    {{ content }}
{% endblock %}
```

If you refresh your homepage now, you should see the menu. But if you go to a subpage, the menu is not there but the `<hr>` line is. 

The problem is that we are only passing the `menuItems` to the homepage. Doing that over and over again for all pages would be a bit tedious and a lot of work if something changes. So let's fix that in the next step.

We could create a global variable that is usable by all templates, but that is not a good idea here. We will add different parts of the site in the future like an admin area and we will have a different menu there.

So instead we will use a custom renderer for the frontend. First we create an empty interface that extends the existing `Renderer` interface. 

```php
<?php declare(strict_types = 1);

namespace Example\Template;

interface FrontendRenderer extends Renderer {}
```

By extending it we are saying that any class implementing the `FrontendRenderer` interface can be used where a `Renderer` is required. But not the other way around, because the `FrontendRenderer` can have more functionality as long as it still fulfills the `Renderer` interface.

Now of course we also need a class that implements the new interface.


```php
<?php declare(strict_types = 1);

namespace Example\Template;

class FrontendTwigRenderer implements FrontendRenderer
{
    private $renderer;

    public function __construct(Renderer $renderer)
    {
        $this->renderer = $renderer;
    }

    public function render($template, $data = []) : string
    {
        $data = array_merge($data, [
            'menuItems' => [['href' => '/', 'text' => 'Homepage']],
        ]);
        return $this->renderer->render($template, $data);
    }
}
```

As you can see we have a dependency on a `Renderer` in this class. This class is a wrapper for our `Renderer` and adds the `menuItems` to all `$data` arrays.

Of course we also need to add another alias to the dependencies file.

```php 
$injector->alias('Example\Template\FrontendRenderer', 'Example\Template\FrontendTwigRenderer');
```

Now go to your controllers and exchange all references of `Renderer` with `FrontendRenderer`. Make sure you change it in both the `use` statement at the top and in the constructor.

Also delete the following line from the `Homepage` controller:

```php
'menuItems' => [['href' => '/', 'text' => 'Homepage']],
```

Once that is done, you should see the menu on both the homepage and your subpages.

Everything should work now, but it doesn't really make sense that the menu is defined in the `FrontendTwigRenderer`. So let's refactor that and move it into it's own class.

Right now the menu is defined in the array, but it is very likely that this will change in the future. Maybe you want to define it in the database or maybe you even want to generate it dynamically based on the pages available. We don't have this information and things might change in the future.

So let's do the right thing here and start with an interface again. But first, create a new folder in the `src` directory for the menu related things. `Menu` sounds like a reasonable name, doesn't it?

```php
<?php declare(strict_types = 1);

namespace Example\Menu;

interface MenuReader
{
    public function readMenu() : array;
}
```

And our very simple implementation will look like this:

```php
<?php declare(strict_types = 1);

namespace Example\Menu;

class ArrayMenuReader implements MenuReader
{
    public function readMenu() : array
    {
        return [
            ['href' => '/', 'text' => 'Homepage'],
        ];
    }
}
```

This is only a temporary solution to keep things moving forward. We are going to revisit this later.

Before we continue, let's edit the dependencies file to make sure that our application knows which implementation to use when the interface is requested.

Add these lines above the `return` statement:

```php
$injector->alias('Example\Menu\MenuReader', 'Example\Menu\ArrayMenuReader');
$injector->share('Example\Menu\ArrayMenuReader');
```

Now you need to change out the hardcoded array in the `FrontendTwigRenderer` class to make it use our new `MenuReader` instead. Give it a try without looking at the solution below.

Did you finish it or did you get stuck? Or are you just lazy? Doesn't matter, here is a working solution:

```php
<?php declare(strict_types = 1);

namespace Example\Template;

use Example\Menu\MenuReader;

class FrontendTwigRenderer implements FrontendRenderer
{
    private $renderer;
    private $menuReader;

    public function __construct(Renderer $renderer, MenuReader $menuReader)
    {
        $this->renderer = $renderer;
        $this->menuReader = $menuReader;
    }

    public function render($template, $data = []) : string
    {
        $data = array_merge($data, [
            'menuItems' => $this->menuReader->readMenu(),
        ]);
        return $this->renderer->render($template, $data);
    }
}
```

Everything still working? Awesome. Commit everything and move on to the next chapter.

[<< previous](10-dynamic-pages.md) | [next >>](12-frontend.md)
