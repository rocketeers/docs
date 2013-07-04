# Setup

Before anything : **Rocketeer requires Laravel 4.1 as it uses the new _illuminate/remote_ component**. It can be used on Laravel 4.0 but requires a little more setup, see the steps

## Adding the package

### With Package Installer

Simply do this :

```
artisan package:install anahkiasen/rocketeer
artisan config:publish anahkiasen/rocketeer
```

### Manually

#### If you're using Laravel 4.0

If you're on 4.0 you have a few extra steps as you need to setup the remote component first. If you're on 4.1 you can skip to the next step.

First, add this to you `composer.json` fime :

```json
"vendor/illuminate/remote"
```

Run update, then add this to your `app/config/app.php` file in the `providers` array :

```php
'Illuminate\Remote\RemoteServiceProvider',
```

#### On Laravel 4.1

Now add the following to your `composer.json` file :

```json
"anahkiasen/rocketeer": "dev-master"
```

And these lines to the `providers` array in your `app/config/app.php` file :

```php
'Rocketeer\RocketeerServiceProvider',
```

Then, this line to the `aliases` array in your `app/config/app.php` file :

```php
'Rocketeer' => 'Rocketeer\Facades\Rocketeer',
```

Then publish the config :

```
artisan config:publish anahkiasen/rocketeer
```

## Configuration

There are two files to configure in order to use Rocketeer.

### app/config/remote.php

If you're on Laravel 4.0, you won't have this file and will need to create it. Here is what you need to put in it by default : https://github.com/laravel/laravel/blob/develop/app/config/remote.php

Now this first file is fairly easy : there are a lot of stuff but if you're familiar with them and/or only have one server to deploy to, you can ignore most of it.
What you want to edit basically is those three lines :

```php
  'production' => array(
    'host'     => 'myserver.fr',
    'username' => 'myusername',
    'password' => 'mypassword',
    'key'      => '',
  ),
```

### app/config/packages/anahkiasen/rocketeer/config.php

Now this one is a little more... well there is a lot more to it, but trust me it's pretty straightforward. Take your time to read the comments about what each of the configuration option mean.
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

  // Git Repository
  //////////////////////////////////////////////////////////////////////

  'git' => array(

    // The SSH/HTTPS adress to your Git Repository
    'repository' => 'https://github.com/facebook/facebook.git',

    // The repository credentials : you can leave those empty
    // if you're using SSH or if your repository is public
    // In other cases you can leave this empty too, and you will
    // be prompted for the credentials on deploy
    'username'   => 'foo',
    'password'   => 'bar',

    // The branch to deploy
    'branch'     => 'master',
  ),

  // Stages
  //
  // The multiples stages of your application – if you don't know
  // what this does, then you don't need it
  //////////////////////////////////////////////////////////////////////

  'stages' => array(

    // Adding entries to this array will split the remote folder in stages
    // Like /var/www/yourapp/staging and /var/www/yourapp/production
    'stages' => array('staging', 'production'),

    // The default stage to execute tasks on when --stage is not provided
    'default' => 'staging',
  ),

  // Remote server
  //////////////////////////////////////////////////////////////////////

  'remote' => array(

    // The root directory where your applications will be deployed
    'root_directory'   => '/home/www/',

    // The name of the application to deploy
    // This will create a folder of the same name in the root directory
    // configured above
    'application_name' => 'facebook',

    // The number of releases to keep at all times
    'keep_releases'    => 4,

    // A list of folders/file to be shared between releases
    // Use this to list folders that need to keep their state, like
    // user uploaded data, file-based databases, etc.
    'shared'           => array(
      'app/database/production.sqlite',
      'public/users/avatars',
    ),
  ),

  // Tasks
  //
  // Here you can define in the `before` and `after` array, Tasks to execute
  // before or after the core Rocketeer Tasks. You can either put a simple command,
  // a closure which receives a $task object, or the name of a class extending
  // the Rocketeer\Tasks\Abstracts\Task class
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
      'setup'   => array(
        'composer install',
        'php artisan basset:build',
      ),
      'deploy'  => array(),
      'cleanup' => array(),
    ),

    // Custom Tasks to register with Rocketeer
    'custom' => array(),
  ),

);
```