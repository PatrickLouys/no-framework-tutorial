[<< previous](01-front-controller.md) | [next >>](03-error-handler.md)

### Composer

[Composer](https://getcomposer.org/) is a dependency manager for PHP.

Just because you are not using a framework does not mean you will have to reinvent the wheel every time you want to do something. With Composer, you can install third-party libraries for your application.

If you don't have Composer installed already, head over to the website and install it. You can find Composer packages for your project on [Packagist](https://packagist.org/).

Create a new file in your project root folder called `composer.json`. This is the Composer configuration file that will be used to configure your project and its dependencies. It must be valid JSON or Composer will fail.

Add the following content to the file:

```json
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
        "php": ">=7.0.0"
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

Composer creates a `composer.lock` file that locks in your dependencies and a vendor directory. 

Committing the `composer.lock` file into version control is generally good practice for projects. It allows continuation testing tools (such as [Travis CI](https://travis-ci.org/)) to run the tests against the exact same versions of libraries that you're developing against. It also allows all people who are working on the project to use the exact same version of libraries i.e. it eliminates a source of "works on my machine" problems.

That being said, [you don't want to put the actual source code of your dependencies in your git repository](https://getcomposer.org/doc/faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md). So let's add a rule to our `.gitignore` file:

```
vendor/
```

Now you have successfully created an empty playground which you can use to set up your project.

[<< previous](01-front-controller.md) | [next >>](03-error-handler.md)
