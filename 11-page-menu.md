[<< previous](10-dynamic-pages.md)

### Page Menu

Now we have some sweet dynamic pages. But nobody can find them.

Let's fix that now. In this chapter we will create a menu with links to all our pages.

For a start we will just send a hardcoded array to the template. Go to you `Homepage` controller and change your `$data` array to this:

```php
$data = [
    'name' => $this->request->getParameter('name', 'stranger'),
    'menuItems' => ['href' => '/', 'text' => 'Homepage'],
];
```

Now add the following at the top of your `Homepage.mustache` file:

```
{{#menuItems}}
    <a href="{{ href }}">{{ text }}</a><br>
{{/menuItems}}
```

Now if you navigate to your homepage, you should see a link at the top.

So far so good. But now we realize that we want to reuse this code snippet on every page. We could create a separate file and include it every time, but there is a better solution.


[<< previous](10-dynamic-pages.md)