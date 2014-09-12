## Creating a PHP application without a framework

### Why this tutorial?

I saw a lot of people coming into the stackoverflow PHP chatroom that asked if X framework is any good. Most of the time the answer was that they should just use PHP and not a framework to build their application. But many are overwhelmed by this and don't know where to start.

So my goal with this is to provide an easy resource that people can be pointed to. In most cases a framework does not make sense and writing an application from scratch with the help of some 3rd party packages is much, much easier than some people think.

So let's get started right away.

### Setting up the front controller

To start, create an empty directory for your project. You also need an entry point where all requests will go to. This means you will have to create an `index.php` file.

A common way to do this is to just put the `index.php` in the root folder of the projects. This is also how some frameworks do it. Let me explain why you should not do this.

The `index.php` is the starting point, so it has to be inside the webserver directory. Which means that the webserver has access to all subdirectories. If you set things up properly, you can still prevent it from accessing your subfolders where your application files are.

But sometimes things don't go according to plan. And if something goes wrong and your files are set up as explained above, your whole application source code could get exposed to visitors. I won't have to explain why this is not a good thing.

So instead of doing that, create a folder in your project folder called `public`. This is a good time to create an `src` folder for your application, also in the project root folder.

Inside the `public` folder you can now create your `index.php`. Remember that you don't want to expose anything here, so put just the following code in there:

```
<?php 

require '../src/Bootstrap.php';
```

The `Bootstrap.php` will be the file that wires your application together. We will get to it shortly.

The rest of the public folder is reserved for your public asset files (like javascript files and stylesheets).

Now navigate inside your `src` folder and create a new `Bootstrap.php` file with the following content:

```
<?php 

echo 'Hello World!';
```

Now let's see if everything is set up correctly. Open up a console and navigate into your projects `public` folder. In there type `php -S localhost:8000` and press enter. This will start the built-in webserver and you can access your page in a browser with `http://localhost:8000`. You should now see the hello world message.

If there is an error, go back and try to fix it. If you only see a blank page, you will likely have to enable error reporting.

Now would be a good time to commit your progress. If you are not already using git, set up a repository now. This is not a git tutorial so I won't go over the details. But using version control should be a habit, even if it is just for a tutorial project like this.

What you have set up now is called a `front controller`. All requests are going to the same file that then decides what to do with it. This is a very common software design pattern.

### Introducing composer

Just because you are not using a framework does not mean you will have to reinvent the wheel every time you want to do something. For PHP there is a dependency manager called composer that you can use to pull in 3rd party packages for your application.

If you don't have composer installed already, head over to the website and install it.

Create a new file in your project root folder called `composer.json`. This is the composer configuration file that will be used to configure your project and it's dependencies. It must be valid json or composer will fail.

Add the following content to the file:

```
{
    "name": "Project name",
    "description": "Your project description",
    "keywords": ["Your keyword", "Another keyword"],
    "license": "MIT",
    "authors": [
        {
            "name": "Your Name",
            "email": "your@email.com",
            "role": "Creator / Main Developer"
        }
    ],
    "require": {
        "php": ">=5.5.0"
    },
    "autoload": {
        "psr-4": {
            "Example\\": "src/"
        }
    }
}
```

In the autoload part you can see that I am using the `Example` namespace for the project. You can use whatever fits your project there, but from now on I will always use the `Example` namespace in my examples. Just replace it with your namespace in your own code.

Open a new console window and navigate into your project root folder. There run `composer update`.

Composer creates a `composer.lock` file that locks in your dependencies and a vendor directory. To remove those from your git repository, add a new file in your project root folder called `.gitignore` with the following content:

```
composer.lock
vendor/
```

This will exclude the included file and folder from your commits. For which would be a good time now by the way.
