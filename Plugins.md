# Plugins

You can add functionalities to Rocketeer or simply bundle common tasks into reusable modules by using the plugins system. A plugin at its core is a class implementing the `Rocketeer\Traits\Plugin` abstract.

## Adding a plugin

To add a plugin to an application, you simply need to add it via Composer and call `Rocketeer::plugin`. You can pass in configuration as the second parameter :

**tasks.php**
```php
Rocketeer::plugin('Rocketeer\Plugins\Campfire', array(
  'domain' => 'MyDomain',
  'room'   => 1234,
));
```

If you're on Laravel, you simply need to add the plugin's `ServiceProvider` to your application which will do this for you. You can then publish the package's configuration in `app/config/packages` by doing `artisan config:publish rocketeer/my-plugin`.

## Creating a plugin

There's two methods a plugin will most likely have on its class : `register(Container $app)` and `onQueue(TasksQueue $queue)`.

- The first one will be used to bind eventual instances into Rocketeer's container, that is a facultative method that if overrided needs to return the Container at the end.
- The second one is used to add actions or tasks to Rocketeer : the **TasksQueue** class is the one behing the Rocketeer facade so most of the methods you're familiar with are available on it : `$queue->before('deploy', ...)`, `$queue->add('MyCustomTask')` etc.

Here is an example dumbed-down version of the current Campfire plugin, using `rcrowe/Campfire` as a dependency :

```php
use rcrowe\Campfire;
use Rocketeer\TasksQueue;
use Rocketeer\Traits\Plugin;

class RocketeerCampfire extends Plugin
{
  /**
   * Bind additional classes to the Container
   *
   * @param Container $app
   *
   * @return void
   */
  public function register(Container $app)
  {
    $app->bind('campfire', function ($app) {
      return new Campfire(['domain' => '...', 'key' => '...', 'room' => '...']);
    });

    return $app;
  }

  /**
   * Register Tasks with Rocketeer
   *
   * @param TasksQueue $queue
   *
   * @return void
   */
  public function onQueue(TasksQueue $queue)
  {
    $queue->after('deploy', function ($task) {
      $application = $task->rocketeer->getApplicationName();

      $task->campfire->send($application. ' was deployed!');
    });
  }
}
```

As you can see a plugin can be something _really_ simple you can save up somewhere and reuse from project to project.

## Plugin configurations

Plugins can have their own configuration, by creating a `config` folder in your plugin's `src` folder. You'll need to set the path to it on your class, in the constructor per example :

```php
function __construct()
{
  $this->configurationFolder = __DIR__.'/../../config';
}
```

In that folder you can then create a `config.php` file to put your options as a PHP array. The configuration for your plugin will then be available via the Config class in your tasks, under the `my-plugin::` namespace, per example if your class is `RocketeerHipchat`, you'll get the configuration by doing `$task->config->get('rocketeer-hipchat::myoption')`.

### On Laravel

Note, if you're in a Laravel application, you can add a plugin's `ServiceProvider` to it and then do `artisan config:publish rocketeer/the-plugin` to get an overridable configuration file in `app/config/packages`.