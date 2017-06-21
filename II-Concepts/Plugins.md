# Plugins

You can add functionalities to Rocketeer or simply bundle common tasks into reusable modules by using the plugins system. A plugin at its core is a class implementing the `Rocketeer\Abstracts\AbstractPlugin` abstract.

## Adding a plugin

To add a plugin, you need to call the `rocketeer plugin:install <package>` command, per example `rocketeer plugin:install anahkiasen/rocketeer-slack`, the package being the Packagist/Github handle of the package.

Once this is done, add the plugin's class to the `plugins` array in `.rocketeer/config.php`:

```php
'plugins' => array(
	'Rocketeer\Plugins\Slack\RocketeerSlack',
),
```

Then, in most cases you'll need to configure said plugin. For this you'll want to publish its configuration in user land via the `rocketeer plugin:config <package>` command. Here we'll call `rocketeer plugin:config anahkiasen/rocketeer-slack` per example.

This will create the `.rocketeer/plugins/rocketeers/rocketeer-slack` folder, with all the plugin's configuration files inside.


## Creating a plugin

There's two methods a plugin will most likely have on its class are `register(Container $app)` and `onQueue(TasksQueue $queue)`.

- The first one will be used to bind eventual instances into Rocketeer's container, that is a facultative method that if overridden needs to return the Container at the end.
- The second one is used to add actions or tasks to Rocketeer : the **TasksQueue** class is the one behind the Rocketeer facade so most of the methods you're familiar with are available on it : `$queue->before('deploy', ...)`, `$queue->add('MyCustomTask')` etc.

Here is an example dumbed-down version of the current Campfire plugin, using `rcrowe/Campfire` as a dependency :

```php
use rcrowe\Campfire;
use Illuminate\Container\Container;
use Rocketeer\Services\Tasks\TasksQueue;
use Rocketeer\Abstracts\AbstractPlugin;

class RocketeerCampfire extends AbstractPlugin
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
public function __construct()
{
  $this->configurationFolder = __DIR__.'/../../config';
}
```

In that folder you can then create a `config.php` file to put your options as a PHP array. The configuration for your plugin will then be available via the Config class in your tasks, under the `my-plugin::` namespace, per example if your class is `RocketeerHipchat`, you'll get the configuration by doing `$task->config->get('rocketeer-hipchat::myoption')`.
