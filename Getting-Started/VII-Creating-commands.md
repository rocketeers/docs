# Creating commands

Ok we created our first few tasks, they work and everything.

But wouldn't it be nice if we could clear the cache at any time, even outside of a deployment? Like, if we had a `rocketeer clear cache` command we could simply call from the terminal? Well **we already do**, all tasks in Rocketeer can easily be made into CLI commands simply by adding them into the `hooks.tasks` array:

**.rocketeer/config/hooks.php**

````php
'tasks' => [
    ClearCache::class,
    'Migrate' => ...
    'Primer' => ...,
],
```

Now run `rocketeer` and there you go:

```shell
$ rocketeer
Available commands:
  check                Check if the server is ready to receive the application
  cleanup              Clean up old releases from the server
  current              Display what the current release is
  ...
 website
  website:clear-cache
````

By default Rocketeer will use a slug of the class's name as CLI command (ie. `ClearCache` => `clear-cache`), but you can configure it by specifying the key:

```php
'tasks' => [
  'clear' => ClearCache::class,
]
```

Or you can also override the `$name` property on the task class (as well as its `$description`) property:

```php
<?php
class ClearCache extends Rocketeer\Tasks\AbstractTask
{
    protected $name = 'clear';
    protected $description = 'Clears the application\'s cache';

    // ...
}
```

Now if we run `rocketeer` again, we see our new name and description:

```
website
 website:clear   Clears the application's cache
```
