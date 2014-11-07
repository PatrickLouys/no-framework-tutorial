[next >>](2-composer.md)

### Front Controller

A [front controller](http://en.wikipedia.org/wiki/Front_Controller_pattern) is a single point of entry for your application.

To start, create an empty directory for your project. You also need an entry point where all requests will go to. This means you will have to create an `index.php` file.

A common way to do this is to just put the `index.php` in the root folder of the projects. This is also how some frameworks do it. Let me explain why you should not do this.

The `index.php` is the starting point, so it has to be inside the web server directory. This means that the web server has access to all subdirectories. If you set things up properly, you can still prevent it from accessing your subfolders where your application files are.

But sometimes things don't go according to plan. And if something goes wrong and your files are set up as above, your whole application source code could be exposed to visitors. I won't have to explain why this is not a good thing.

So instead of doing that, create a folder in your project folder called `public`. This is a good time to create a `src` folder for your application, also in the project root folder.

Inside the `public` folder you can now create your `index.php`. Remember that you don't want to expose anything here, so put just the following code in there:

```php
<?php 

require __DIR__ . '/../src/Bootstrap.php';
```

The `Bootstrap.php` will be the file that wires your application together. We will get to it shortly.

The rest of the public folder is reserved for your public asset files (like JavaScript files and stylesheets).

Now navigate inside your `src` folder and create a new `Bootstrap.php` file with the following content:

```php
<?php 

echo 'Hello World!';
```

Now let's see if everything is set up correctly. Open up a console and navigate into your projects `public` folder. In there type `php -S localhost:8000` and press enter. This will start the built-in webserver and you can access your page in a browser with `http://localhost:8000`. You should now see the 'hello world' message.

If there is an error, go back and try to fix it. If you only see a blank page, check the console window where the server is running for errors.

Now would be a good time to commit your progress. If you are not already using Git, set up a repository now. This is not a Git tutorial so I won't go over the details. But using version control should be a habit, even if it is just for a tutorial project like this.

[next >>](2-composer.md)
