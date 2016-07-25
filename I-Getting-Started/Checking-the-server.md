# Checking the server

Ok Rocketeer is installed and everything, all is right in the world. Now before we begin sending files in the clouds, let's check if it works.

Run `rocketeer check`, if everything went well, it should have recognized the type of your project and the package managers that you use, and checked that the server you're deploying to matches that:

```shell
$ rocketeer check
├─ Check (Check if the server is ready to receive the application)
|  ├─ Checking presence of git
|  ├─ Checking presence of language
|  ├─ Checking presence of package manager
|  ├─ Checking presence of required extensions
|  ├─ Your server is ready to deploy
```

What this means per example is if you have a `composer.json` saying you need PHP 7 and the `pthreads` extension, Rocketeer will check that both are on the server. If you have a `package.json` saying you need Node 6, Rocketeer will check that, etc.

## Customizing paths

If the check failed, first of all it may be because Rocketeer couldn't find the binaries it needed (php, composer) etc. In most cases it should be able to find them automatically but if not, you can always tell him directly where everything is.

For this, let's open `.rocketeer/config/paths.php`. Now imagine that per example your server is running PHP5 but does have PHP7 available under another name, we can tell Rocketeer like this:

**.rocketeer/config/paths.php**

```php
<?php
return [
    'paths' => [
        'php' => '/usr/local/bin/php7'
    ],
];
```

Now every time Rocketeer needs to use PHP in a command, it'll try to use this path first. If not it will try to find it himself and cache the result for later use. Do note that Rocketeer will **blindly trust** whatever path you configure so make sure it is correct.

## Switching strategies

Now, if Rocketeer has improperly guessed the type of your application or some checks fail but you know everything works, you can switch how Rocketeer does its check.

Internally, Rocketeer has what we call _strategies_: different ways to go about a certain task. Let's see what strategies are available for the Check task:

```
$ rocketeer strategies check
```

| Strategy | Implementation | Description                                                             |
|:---------|:---------------|:------------------------------------------------------------------------|
| check    | Php            | Checks if the server is ready to receive a PHP application              |
| check    | Ruby           | Checks if the server is ready to receive a Ruby application             |
| check    | Node           | Checks if the server is ready to receive a Node application             |
| check    | Polyglot       | Checks if the server is ready to receive an application of any language |

By default Rocketeer uses the `Polyglot` strategy, which is a way to say "All of the above if necessary". It'll check if you use Node, and check that if needed, check if you use PHP, etc.

In order to switch strategy, open the `strategies.php` configuration file and simply put the implementation you wish to use next to the strategy type:

**.rocketeer/config/strategies.php**

```
<?php
return [
    'strategies' => [

        // Which strategy to use to check the server
        'check' => 'Php',

        // ...
    ],

];
```

You can also completely disable the strategy by simply putting in a falsey value:

```php
'check' => null,
```

And there you go, Rocketeer will now simply check your SCM is present (if needed):

```php
$ rocketeer check
├─ Check (Check if the server is ready to receive the application) [~1.35s]
|  ├─ Checking presence of git
|  ├─ Your server is ready to deploy
```

Now that everything works let's try to deploy a first time
