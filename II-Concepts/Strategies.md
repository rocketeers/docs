# Strategies

## Introduction

Rocketeer has all the core tasks you'd expect from a deployer: creating a new release, testing it, migrating the database, etc. Most tasks have only one way to do things, but certain core ones have multiple paths they can use to accomplish their goals: those are the strategies.

The most important tasks using strategies are the Deploy and Update ones, by default they use your configured SCM (Git, Svn, etc.) to clone a clean new release on the server, but that's not the only strategy available. You can see what tasks use strategies and what options are available by calling the `rocketeer strategies` command. It'll yield a table similar to the following:

| Strategy        | Implementation | Description                                                                 |
| --------------- | -----------    | --------------------------------------------------------------------------- |
| deploy          | Clone          | Clones a fresh instance of the repository by SCM                            |
| deploy          | Copy           | Copies the previously cloned instance of the repository and update it       |
| deploy          | Sync           | Uses rsync to create or update a release from the local files               |
| test            | Phpunit        | Run the tests with PHPUnit                                                  |
| migrate         | Artisan        | Migrates your database with Laravel's Artisan CLI                           |
| dependencies    | Composer       | Installs dependencies with Composer                                         |
| dependencies    | Bundler        | Installs dependencies with Bundler                                          |
| dependencies    | Npm            | Installs dependencies with NPM                                              |
| dependencies    | Bower          | Installs dependencies with Bower                                            |
| dependencies    | Polyglot       | Runs all of the above package managers if necessary                         |

You can configure which task uses which strategy in the `strategies.php` file of your configuration folder.

## Adding your own strategies

Rocketeer comes with a handful of available implementations for each strategy but sometimes you use a particular tool or procedure that isn't built-in. It's really easy to add your own strategies, there's only two steps really. Say you want to test your application with Grunt instead of PHPunit, in the case of a Node application per example.

First you'll create a class for your strategy extending `Rocketeer\Abstracts\AbstractStrategy`:

```php
<?php
namespace Acme;

use Rocketeer\Abstracts\AbstractStrategy;

class GruntStrategy extends AbstractStrategy
{
  // ...
}
```

Next we need to define which strategy this corresponds to, for this you'll implement the relevant interface from the `Rocketeer\Interfaces\Strategies` namespace, in our case, `TestStrategyInterface`:


```php
<?php
namespace Acme;

use Rocketeer\Abstracts\AbstractStrategy;
use Rocketeer\Strategies\Interfaces\TestStrategyInterface;

class GruntStrategy extends AbstractStrategy implements TestStrategyInterface
{
  public function test()
  {
    // ...
  }
}
```

All that is left to do is write the business logic. Strategies implements the same traits than Tasks so you have all the usual tools available, in particular binaries (see the relevant documentation section for more informations).


```php
<?php
namespace Acme;

use Rocketeer\Abstracts\AbstractStrategy;
use Rocketeer\Strategies\Interfaces\TestStrategyInterface;

class GruntStrategy extends AbstractStrategy implements TestStrategyInterface
{
  public function test()
  {
    return $this->binary('grunt')->runForCurrentRelease('test');
  }
}
```

This will run `grunt test` on the configured connection. All that is left to do is to configure Rocketeer to use that strategy, in the `strategies.php` file:

```php
// Which strategy to use to test your application
'test'         => 'Acme\Grunt',
```
