# Setup

Before anything : **Rocketeer requires Laravel 4.1 as it uses the new _illuminate/remote_ component**. It can be used on Laravel 4.0 but requires a little more setup, see the steps

## Adding the package

### With Package Installer

If you're using [Package Installer](https://github.com/rtablada/package-installer), simply do this :

```
artisan package:install anahkiasen/rocketeer
artisan deploy:ignite
```

### With Laravel

#### If you're using Laravel 4.0

If you're on 4.0 you have a few extra steps as you need to setup the remote component first. If you're on 4.1 you can skip to the next step.

First, add this to you `composer.json` file in the dependencies by typing `composer require illuminate/remote:4.1.*@dev`. Then add this in `classmap` array of your `composer.json` :

```json
"vendor/illuminate/remote"
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

### Outside of Laravel

Outside of Laravel it's (oddly) a little simpler :

```
composer require illuminate/remote:4.1.*@dev
composer require anahkiasen/rocketeer:dev-master
php vendor/bin/rocketeer ignite
```

And you're good to go, the configuration file referenced below will be created at `yourapp/rocketeer.php`. Use `php vendor/bin/rocketeer` to access Rocketeer's commands or simply alias it : `alias rocketeer=php vendor/bin/rocketeer`.

## Configuration

There are two files to configure in order to use Rocketeer.

### app/config/remote.php

If you're on Laravel 4.0, you won't have this file and will need to create it. Here is what you need to [put in it by default](https://github.com/laravel/laravel/blob/develop/app/config/remote.php).

Now this first file is fairly easy : there are a lot of stuff but if you're familiar with them and/or only have one server to deploy to, you can ignore most of it.
What you want to edit basically is those three lines : `host`, `username` and `password`. If you're using an SSH key to connect, ignore password and fill out `key` instead.

```php
  'production' => array(
    'host'     => 'myserver.fr',
    'username' => 'myusername',
    'password' => 'mypassword',
    'key'      => '', # location of public key (/home/xxx/.ssh/id_rsa)
    'keyphrase'=> ''  # password for public key
  ),
```

### app/config/packages/anahkiasen/rocketeer/config.php

Now this one is a little more... well there is a lot more to it, but trust me it's pretty straightforward.

**Take your time to read the comments** about what each of the configuration option mean.
To get you started here is an example config file to make you picture a little more what goes into each field :

```php
<?php return array(

  // Remote access
  //
  // You can either use a single connection or an array of connections
  // For this configure your app/remote.php file
  //////////////////////////////////////////////////////////////////////

  // The remote connection(s) to deploy to
  'connections' => array('production'),

  // SCM repository
  //////////////////////////////////////////////////////////////////////

  'scm' => array(

    // The SCM used (supported: "git")
    'scm' => 'git',

    // The SSH/HTTPS adress to your repository
    // Example: https://github.com/vendor/website.git
    'repository' => '',

    // The repository credentials : you can leave those empty
    // if you're using SSH or if your repository is public
    // In other cases you can leave this empty too, and you will
    // be prompted for the credentials on deploy
    'username'   => '',
    'password'   => '',

    // The branch to deploy
    'branch'     => 'master',
  ),

  // Stages
  //
  // The multiples stages of your application
  // if you don't know what this does, then you don't need it
  //////////////////////////////////////////////////////////////////////

  'stages' => array(

    // Adding entries to this array will split the remote folder in stages
    // Like /var/www/yourapp/staging and /var/www/yourapp/production
    'stages' => array(),

    // The default stage to execute tasks on when --stage is not provided
    'default' => '',
  ),

  // Remote server
  //////////////////////////////////////////////////////////////////////

  'remote' => array(

    // The root directory where your applications will be deployed
    'root_directory'   => '/home/www/',

    // The name of the application to deploy
    // This will create a folder of the same name in the root directory
    // configured above, so be careful about the characters used
    'application_name' => 'application',

    // The number of releases to keep at all times
    'keep_releases'    => 4,

    // A list of folders/file to be shared between releases
    // Use this to list folders that need to keep their state, like
    // user uploaded data, file-based databases, etc.
    'shared' => array(
      '{path.storage}/logs',
      '{path.storage}/sessions',
    ),

    'permissions' => array(

      // The permissions to CHMOD folders to
      'permissions' => 755,

      // The folders and files to set as web writable
      // You can pass paths in brackets, so {path.public} will return
      // the correct path to the public folder
      'files' => array(
        'app/database/production.sqlite',
        '{path.storage}',
        '{path.public}',
      ),

      // The Apache user and group to CHOWN folders to
      // Leave empty to leave the above folders untouched
      'apache' => array(
        'user'  => 'www-data',
        'group' => 'www-data',
      ),

    ),
  ),

  // Tasks
  //
  // Here you can define in the `before` and `after` array, Tasks to execute
  // before or after the core Rocketeer Tasks. You can either put a simple command,
  // a closure which receives a $task object, or the name of a class extending
  // the Rocketeer\Traits\Task class
  //
  // In the `custom` array you can list custom Tasks classes to be added
  // to Rocketeer. Those will then be available in Artisan
  // as `php artisan deploy:yourtask`
  //////////////////////////////////////////////////////////////////////

  'tasks' => array(

    // Tasks to execute before the core Rocketeer Tasks
    'before' => array(
      'setup'   => array(),
      'deploy'  => array(),
      'cleanup' => array(),
    ),

    // Tasks to execute after the core Rocketeer Tasks
    'after' => array(
      'setup'   => array(),
      'deploy'  => array(),
      'cleanup' => array(),
    ),

    // Custom Tasks to register with Rocketeer
    'custom' => array(),
  ),

  'on' => array(

		// Stages configurations
		'stages' => array(
		),

		// Connections configuration
		'connections' => array(
		),

	),


);
```

## Setup

After that it is recommended to run the `artisan deploy:check` command, it will run various commands on the server to check whether the latter is ready to receive your application.

Once you're done, you can just hit `artisan deploy:deploy`. It will create an initial release on your remote server.
Afterwards, to update it you can either run the same command again which will create an entirely new release, or simply do an `artisan deploy:update` which will update the repository and dependencies of your application.