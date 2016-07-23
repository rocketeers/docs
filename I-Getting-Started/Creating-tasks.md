# Creating tasks

Now that we've informed Rocketeer on how our application is structure, it's time to tell it what to do to deploy it. Now, by default is pretty smart and will know how to install your dependencies and stuff, but that's not all is it?

Let's review how to do various common things to prepare a release for use in production.

## Database operations

Most common task you can create: database stuff. Rocketeer already comes with a Migrate task but by default it does nothing, so just like we did for `Primer`, we can override it in `hooks.php`.

Imagine we're running a Laravel application, we would need to call `php artisan migrate --force`, and maybe we have a backup command to call beforehand, so let's write our task as such:

**.rocketeer/config/hooks.php**

```php
'tasks' => [
    'Migrate' => function (AbstractTask $task) {
        return $task->runForApplication([
            'php artisan migrate --force',
            'php artisan db:backup',
        ]);
    },
],
```
