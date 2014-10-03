# Laravel integration

Because it was originally a Laravel package and uses some of its components, Rocketeer provides deep integration with the framework. Here is how to setup the package with it.

## Installation

Start by adding Rocketeer as one of your dev-dependencies with the following command:

```bash
$ composer require --dev "anahkiasen/rocketeer:~2.0.*"
```

Once this is done, you'll need to add Rocketeer's provider to your application's in `app/config/local/app.php`:

```php
'providers' => array(
	// ...
	'Rocketeer\RocketeerServiceProvider',
),
```

Then, this line to the `aliases` array in your `app/config/local/app.php` file :

```php
'Rocketeer' => 'Rocketeer\Facades\Rocketeer',
```

Ensuring that these changes are made in your 'local' environment configuration since Rocketeer has been included as a dev-dependency.

## Setting up the project

Now that you added Rocketeer to the application, if you hit `artisan list` you should see all of the deployments commands bound to the `deploy:` namespace. That means instead of doing `rocketeer update` per example you will do `artisan deploy:update`.
There is however an alias added for convenience which allows you to do `artisan deploy` and run the Deploy task.

Now hit the `artisan deploy:ignite` command. This will publish Rocketeer's configuration to `app/config/packages/anahkiasen/rocketeer`, in there you will find all the usual files.

## Additions

Now that this is done you can safely define tasks, strategies or binaries in your application's namespace and they will be recognised by Rocketeer. It will also recognise connections defined in `app/config/remote.php` and merge them with any connection defined in its own configuration.

You can also register any additional plugin simply by adding its provider to the application's providers array. The plugin's configuration can also be publish by doing the usual `artisan config:publish vendor/package`