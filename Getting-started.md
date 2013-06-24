# Setup

Before anything : **Rocketeer requires Laravel 4.1 as it uses the new _illuminate/remote_ component**.
Now if you fit that requirement, you're good to go. There are two ways to setup Rocketeer :

## Adding the package

### With Package Installer

Simply do this :

```
artisan package:install anahkiasen/rocketeer
artisan config:publish anahkiasen/rocketeer
```

### Manually

To use it, add the following to your `composer.json` file :

```json
"anahkiasen/rocketeer": "dev-master"
```

And this line to the `providers` array in your `app/config/app.php` file :

```php
'Rocketeer\RocketeerServiceProvider',
```

And this line to the `aliases` array in your `app/config/app.php` file :

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

This first one is fairly easy : there are a lot of stuff but if you're familiar with them and/or only have one server to deploy to, you can ignore most of it.
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
  'connections' => 'production',

  // Git Repository
  //////////////////////////////////////////////////////////////////////

  'git' => array(

    // The SSH/HTTPS adress to your Git Repository
    'repository' => 'https://bitbucket.org/myUsername/facebook.git',

    // Its credentials
    'username'   => 'myUsername',
    'password'   => 'myPassword',

    // The branch to deploy
    'branch'     => 'master',
  ),

  // Remote server
  //////////////////////////////////////////////////////////////////////

  'remote' => array(

    // The root directory where your applications will be deployed
    'root_directory'   => '/home/www/',

    // The default name of the application to deploy
    'application_name' => 'facebook',

    // The number of releases to keep at all times
    'releases' => 4,
  ),

  // Tasks
  //////////////////////////////////////////////////////////////////////

  // Here you can define custom tasks to execute after certain actions
  'tasks' => array(

    // Tasks to execute before commands
    'before' => array(),

    // Tasks to execute after commands
    'after' => array(
      'deploy'  => array(
        'bower install',
        'php artisan basset:build'
      ),
    ),
  ),

);
```