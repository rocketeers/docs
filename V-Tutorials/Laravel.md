[translation here]

<!--original
# Laravel integration
-->

[translation here]

<!--original
Because it was originally a Laravel package and uses some of its components, Rocketeer provides deep integration with the framework. Here is how to setup the package with it.
-->

[translation here]

<!--original
## Installation
-->

[translation here]

<!--original
Start by adding Rocketeer as one of your dev-dependencies with the following command:
-->

```bash
$ composer require --dev anahkiasen/rocketeer
```

[translation here]

<!--original
Once this is done, you'll need to add Rocketeer's provider to your application's in `app/config/local/app.php` file:
-->

```php
'providers' => append_config(array(
	// ...
	'Rocketeer\RocketeerServiceProvider',
)),
```

[translation here]

<!--original
Then, this line to the `aliases` array in your `app/config/local/app.php` file:
-->

```php
'aliases' => append_config(array(
	// ...
	'Rocketeer' => 'Rocketeer\Facades\Rocketeer',
)),
```

[translation here]

<!--original
Ensuring that these changes are made in your 'local' environment configuration using the [`append_config()`][0] helper function since Rocketeer has been included as a dev-dependency.
-->
 
[translation here]

<!--original
## Setting up the project
-->

[translation here]

<!--original
Now that you added Rocketeer to the application, if you hit `artisan list` you should see all of the deployments commands bound to the `deploy:` namespace. That means instead of doing `rocketeer update` per example you will do `artisan deploy:update`.
There is however an alias added for convenience which allows you to do `artisan deploy` and run the Deploy task.
-->

[translation here]

<!--original
Now hit the `artisan deploy:ignite` command. This will publish Rocketeer's configuration to `app/config/packages/anahkiasen/rocketeer`, in there you will find all the usual files.
-->

[translation here]

<!--original
## Additions
-->

[translation here]

<!--original
Now that this is done you can safely define tasks, strategies or binaries in your application's namespace and they will be recognised by Rocketeer. It will also recognise connections defined in `app/config/remote.php` and merge them with any connection defined in its own configuration.
-->

[translation here]

<!--original
You can also register any additional plugin simply by adding its provider to the application's providers array. The plugin's configuration can also be publish by doing the usual `artisan config:publish vendor/package`
-->

[translation here]

<!--original
[0]: http://laravel.com/docs/4.2/configuration#provider-configuration
-->
