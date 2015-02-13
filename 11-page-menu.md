[<< previous](10-dynamic-pages.md)

### Page Menu

Now we have some sweet dynamic pages. But nobody can find them.

Let's fix that now. In this chapter we will create a menu with links to all our pages.

When we have a menu, we will want to be able to reuse the same code on multiple page. We could create a separate file and include it every time, but there is a better solution.

It is more practical to have templates that are able to extend other templates, like a layout for example. Then we can have all the layout related code in a single file and we don't have to include header and footer files in every template.

Sadly our implementation of mustache does not support this. We could write code to work around this, which will take time and could introduce some bugs. Or we could switch to a library that already supports this and is well tested. [Twig](http://twig.sensiolabs.org/) for example.

Now you might wonder why we didn't start with Twig right away. This is a good example to show why using interfaces and writing loosely-coupled code is a good idea.

Remember how you created a `MustacheRenderer` in [chapter 9](09-templating.md)? This time, we create a `TwigRenderer` that implements the same interface.

But before we start, install the latest version of Twig with composer.

Then create the a `TwigRenderer.php` in your `src/Template` folder that looks like this:

```php 
<?php

namespace Example\Template;

use Twig_Environment;

class TwigRenderer implements Renderer
{
    private $renderer;

    public function __construct(Twig_Environment $renderer)
    {
        $this->renderer = $renderer;
    }

    public function render($template, $data = [])
    {
        return $this->renderer->render("$template.html", $data);
    }
}
```

As you can see, on the render function call a `.html` is added. This is because Twig does not add a file ending by default and you would have to specifiy it on every call otherwise. By doing it like this, you can use it in the same way as you used Mustache.

Add the following code to your `Dependencies.php` file: 

```php
$injector->delegate('Twig_Environment', function() use ($injector) {
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
    'menuItems' => 'menuItems' => [['href' => '/', 'text' => 'Homepage']],
];
```

At the top of your `Homepage.html` file add this code:

```
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

to be continued...

[<< previous](10-dynamic-pages.md)