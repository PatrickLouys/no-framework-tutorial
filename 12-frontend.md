[<< previous](11-page-menu.md) | [next >>](to-be-continued.md)


### Frontend

I don't know about you, but I don't like to work on a site that looks two decades old. So let's improve the look of our little application.

This is not a frontend tutorial, so we'll just use [pure](http://purecss.io/) and call it a day.

First we need to change the `Layout.html` template. I don't want to waste your time with HTML and CSS, so I'll just provide the code for you to copy paste.

```php
<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>Example</title>
        <link rel="stylesheet" href="http://yui.yahooapis.com/pure/0.6.0/pure-min.css">
        <link rel="stylesheet" href="/css/style.css">
    </head>
    <body>
        <div id="layout">
            <div id="menu">
                <div class="pure-menu">
                    <ul class="pure-menu-list">
                        {% for item in menuItems %}
                            <li class="pure-menu-item"><a href="{{ item['href'] }}" class="pure-menu-link">{{ item['text'] }}</a></li>
                        {% endfor %}
                    </ul>
                </div>
            </div>
            <div id="main">
                <div class="content">
                    {% block content %}
                    {% endblock %}
                </div>
            </div>
        </div>
    </body>
</html>
```

You will also need some custom CSS. This is a file that we want publicly accessible. So where do we put it? Exactly, in the public folder.

But to keep things a little organized, add a `css` folder in there first and then create a `style.css` with the following content:

```css
body {
    color: #777;
}

#layout {
    position: relative;
    padding-left: 0;
}

#layout.active #menu {
    left: 150px;
    width: 150px;
}

#layout.active .menu-link {
    left: 150px;
}

.content {
    margin: 0 auto;
    padding: 0 2em;
    max-width: 800px;
    margin-bottom: 50px;
    line-height: 1.6em;
}

.header {
    margin: 0;
    color: #333;
    text-align: center;
    padding: 2.5em 2em 0;
    border-bottom: 1px solid #eee;
}

.header h1 {
    margin: 0.2em 0;
    font-size: 3em;
    font-weight: 300;
}

.header h2 {
    font-weight: 300;
    color: #ccc;
    padding: 0;
    margin-top: 0;
}

#menu {
    margin-left: -150px;
    width: 150px;
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    z-index: 1000; 
    background: #191818;
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;
}

#menu a {
    color: #999;
    border: none;
    padding: 0.6em 0 0.6em 0.6em;
}

#menu .pure-menu,
#menu .pure-menu ul {
    border: none;
    background: transparent;
}

#menu .pure-menu ul,
#menu .pure-menu .menu-item-divided {
    border-top: 1px solid #333;
}

#menu .pure-menu li a:hover,
#menu .pure-menu li a:focus {
    background: #333;
}

#menu .pure-menu-selected,
#menu .pure-menu-heading {
    background: #1f8dd6;
}

#menu .pure-menu-selected a {
    color: #fff;
}

#menu .pure-menu-heading {
    font-size: 110%;
    color: #fff;
    margin: 0;
}

.header,
.content {
    padding-left: 2em;
    padding-right: 2em;
}

#layout {
    padding-left: 150px; /* left col width "#menu" */
    left: 0;
}
#menu {
    left: 150px;
}

.menu-link {
    position: fixed;
    left: 150px;
    display: none;
}

#layout.active .menu-link {
    left: 150px;
}
```

Now if you have a look at your site again, things should look a little better. Feel free to further improve the look of it yourself later. But let's continue with the tutorial now.

Every time that you need an asset or a file publicly available, then you can just put it in your `public` folder. You will need this for all kinds of assets like javascript files, css files, images and more.

So far so good, but it would be nice if our visitors can see what page they are on.

Of course we need more than one page in the menu for this. I will just use the `page-one.md` that we created earlier in the tutorial. But feel free to add a few more pages and add them as well.

Go back to the `ArrayMenuReader` and add your new pages to the array. It should look something like this now:

```php
return [
    ['href' => '/', 'text' => 'Homepage'],
    ['href' => '/page-one', 'text' => 'Page One'],
];
```

[<< previous](11-page-menu.md) | [next >>](to-be-continued.md)

