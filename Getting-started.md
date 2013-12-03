# Setup

Installing Rocketeer on a project is quite easy, there are several ways to do so.

## Via the compiled archive

The easiest way is to just get the archive by doing downloading it [at the following address](http://rocketeer.autopergamene.eu/versions/rocketeer) and putting it in your application's directory.
Then you just need to type `php rocketeer ignite`. You'll get asked a series of question to setup your project and you show be good to go.

## With Composer

You also install Rocketeer with Composer as any package :

```
composer require illuminate/remote:4.1.*@dev
composer require anahkiasen/rocketeer:dev-master
php vendor/bin/rocketeer ignite
```

And you're good to go, the configuration file referenced below will be created at `yourapp/rocketeer.php`. Use `php vendor/bin/rocketeer` to access Rocketeer's commands or simply alias it : `alias rocketeer=php vendor/bin/rocketeer`.

## Integration with Laravel

Rocketeer integrates nicely with Laravel, although you'll need to do some more steps, depending on whether you're using Laravel 4.0 or 4.1 :

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

You'll do most of your configuration in Rocketeer's configuration file, `rocketeer.php`.
If you're on Laravel and added the package as a dependency, you'll instead need to configure `app/config/packages/anahkiasen/rocketeer/config.php`.

Rocketeer recognizes Laravel's `remote.php` file so you can configure your connections here if you want, Rocketeer will read it first before falling back to the configuration file.

There are a lot of options in the configuration file, each is explained thoroughly so take your time to read the comments carefully.

## Using Rocketeer

If you're using the archive, you'll need to hit `php rocketeer` to access the commands, if you're on Laravel and installed it as a package, you'll want to hit the `php artisan deploy:` namespace. Per example the `update` command would be accessed as `php rocketeer update` with the archive, and `php artisan deploy:update` with the package.

Once you're done with the configuration, it is recommended to run the `php rocketeer check` command, it will run various commands on the server to check whether the latter is ready to receive your application.

Once you're done, you can just hit `php rocketeer deploy`. It will create an initial release on your remote server.
Afterwards, to update it you can either run the same command again which will create an entirely new release, or simply do an `php rocketeer update` which will update the repository and dependencies of your application.

## Folder organization

Rocketeer recognizes its custom configuration (`rocketeer.php`) from where Rocketeer itself is. Same thing for the custom `tasks.php` file, so you could have that organization :

```
| deploy
| -- rocketeer
| -- rocketeer.php
| -- tasks.php
| index.php
| public
| etc.
```