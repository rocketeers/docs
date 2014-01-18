# Setup

Installing Rocketeer on a project is quite easy, there are several ways to do so.

## Via the compiled archive

The easiest way is to just get the archive by doing downloading it [at the following addresss](http://rocketeer.autopergamene.eu/versions/rocketeer.phar) and putting it in your application's directory.

You can also install it globally by doing `mv rocketeer.phar /usr/local/bin/rocketeer` which will then give you a global `rocketeer` command to use in any folder.

Then you just need to type `rocketeer ignite`. You'll get asked a series of question to setup your project and you show be good to go.

## With Composer

You can also install Rocketeer with Composer as any package :

```
composer require illuminate/remote:4.1.*@dev
composer require anahkiasen/rocketeer:dev-master

php vendor/bin/rocketeer ignite
```

And you're good to go, the configuration file referenced below will be created at `yourapp/.rocketeer/`.

Use `php vendor/bin/rocketeer` to access Rocketeer's commands or simply alias it : `alias rocketeer=php vendor/bin/rocketeer`. You can also add `vendor/bin` to your _PATH_ to always have local binaries accessible.

## Integration with Laravel

Rocketeer integrates nicely with Laravel, although you'll need to do some more steps, depending on whether you're using Laravel 4.0 or 4.1. To check what version you're using, just type `php artisan --version` in your terminal.

#### If you're using Laravel 4.0

If you're on 4.0 you have a few extra steps as you need to setup the remote component first. If you're on 4.1 you can skip to the next step.

First, add this to you `composer.json` file in the `require` section :

```json
"illuminate/remote": "dev-master",
```

Then add this at the end of your `composer.json` :

```json
"repositories": [
  {
    "type": "vcs",
    "url": "https://github.com/changwuf31/remote"
  }
],
```

Run update, then add this to your `app/config/app.php` file in the `providers` array :

```php
'Illuminate\Remote\RemoteServiceProvider',
```

You will also want to pull in the `remote.php` config file since it did not exist in 4.0. To do so, copy and paste the contents of [develop/app/config/remote.php](https://github.com/laravel/laravel/blob/develop/app/config/remote.php) to your `app/config/remote.php` file.

#### On Laravel 4.0 and 4.1

Now type the following `composer require anahkiasen/rocketeer:dev-master`.

You'll need to add these lines to the `providers` array in your `app/config/app.php` file :

```php
'Rocketeer\RocketeerServiceProvider',
```

Then, this line to the `aliases` array in your `app/config/app.php` file :

```php
'Rocketeer' => 'Rocketeer\Facades\Rocketeer',
```

Then publish the config :

```
artisan deploy:ignite
```

## Configuration

You'll do most of your configuration in Rocketeer's configuration files, in the `.rocketeer` folder.
If you're on Laravel and added the package as a dependency, you'll instead need to configure files in `app/config/packages/anahkiasen/rocketeer/`.

Rocketeer recognizes Laravel's `remote.php` file so you can configure your connections here if you want, Rocketeer will read it first before falling back to the configuration file.

There are a lot of options in the configuration file, each is explained thoroughly so take your time to read the comments carefully.

## Using Rocketeer

If you're using the archive, you'll need to hit `php rocketeer.phar` to access the commands, if you're on Laravel and installed it as a package, you'll want to hit the `php artisan deploy:` namespace. Per example the `update` command would be accessed as `php rocketeer.phar update` with the archive, and `php artisan deploy:update` with the package.

Once you're done with the configuration, it is recommended to run the `php rocketeer.phar check` command, it will run various commands on the server to check whether the latter is ready to receive your application.

Once you're done, you can just hit `php rocketeer.phar deploy`. It will create an initial release on your remote server.
Afterwards, to update it you can either run the same command again which will create an entirely new release, or simply do an `php rocketeer.phar update` which will update the repository and dependencies of your application.

### Folder organization

Rocketeer recognizes its custom configuration in the folder you currently are in. Same thing for the custom `tasks.php` file, so you could have that organization :

```
| .rocketeer
| -- config.php
| -- hooks.php
| -- paths.php
| -- remote.php
| -- scm.php
| -- stages.php
| -- tasks.php
| rocketeer.phar
| index.php
| public
| etc.
```