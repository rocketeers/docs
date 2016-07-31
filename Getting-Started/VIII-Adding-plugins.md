# Adding plugins

Once you've configured the tasks your deployment will need, it would be nice to be notified when the deployment starts and ends (or fails). For this example we'll imagine you have a Slack channel corresponding to your application. There are a bunch of plugins available for Rocketeer, one of which is the `rocketeer-slack` plugin that does just that.

## Installing plugins

To install a plugin we call the `rocketeer plugins:install` command, Rocketeer plugins are just Composer packages and it uses Composer underneath to install and update them. So what we need is the package's name [as seen on Packagist](https://packagist.org/packages/anahkiasen/rocketeer-slack).

Now that we have that, let's run the command:

```shell
$ rocketeer plugins:install anahkiasen/rocketeer-slack
├─ Installer (Installs one or more plugins) [~5.51s]
|  ├─ Installing anahkiasen/rocketeer-slack
```

Underneath Rocketeer will basically run `composer require anahkiasen/rocketeer-slack --working-dir=".rocketeer"`, that's all. So if you check your `.rocketeer` folder again you should now see a `composer.json` file with the dependency added, as well as a `vendor` folder containing the plugin and its dependencies.

## Enabling a plugin

Plugins are – underneath – just service providers to Rocketeer's internal container. To enable a plugin you need to add the name of its service provided in the `plugins.loaded` array – this is usually specified in the package's README. In this case, the name of the class is `Rocketeer\Plugins\Slack\Slack` so let's add that:

**.rocketeer/config/plugins.php**

```php
<?php
return [
    'plugins' => [
        'loaded' => [
            Rocketeer\Plugins\Slack\Slack::class,
        ],
        'config' => [],
    ],
];
```

To verify that a plugin was correctly enabled, run `rocketeer plugins:list`, if it worked you should now see it in the list:

```shell
$ rocketeer plugins:list
-------------------------------
 Plugin
-------------------------------
 Rocketeer\Plugins\Slack\Slack
-------------------------------
```

## Configuring a plugin

To configure the plugin, simply run `rocketeer plugins:config {package}` (in this case `anahkiasen/rocketeer-slack`):

```shell
$ rocketeer plugins:config anahkiasen/rocketeer-slack
Published configuration from .rocketeer/vendor/anahkiasen/rocketeer-slack/src/config to .rocketeer/config/plugins/rocketeer-slack
```

Now let's take a look at it,
