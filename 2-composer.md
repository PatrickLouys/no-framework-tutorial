[<< previous](1-front-controller.md) | [next >>](3-error-handler.md)

### Composer

[Composer](https://getcomposer.org/) is a dependency manager for PHP.

Just because you are not using a framework does not mean you will have to reinvent the wheel every time you want to do something. With composer you can install 3rd party libraries for your application.

If you don't have composer installed already, head over to the website and install it. You can find composer packages for your project on [packagist](https://packagist.org/).

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

Now you have successfully created an empty playground which you can use to set up your project.

[<< previous](1-front-controller.md) | [next >>](3-error-handler.md)