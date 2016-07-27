# Preparing your first deployment

Ok, our server is ready and everything, we've configured our connections and paths. Before you start deploying, let's look at what a deployment _looks like_, without running anything, here is basically what will happen when you will call `rocketeer deploy`:

```shell
$ rocketeer deploy
├─ Deploy (Deploys the website)
│  ├─ Deploy/Rolling (Uses a system of folders current/releases/shared to roll releases)
│  │  ├─ Primer (Run local checks to ensure deploy can proceed)
│  |  ├─ Server is not ready, running Setup task
│  │  ├─ Setup (Set up the remote server for deployment)
│  │  │  ├─ Check (Check if the server is ready to receive the application)
│  │  │  │  ├─ Check/Polyglot (Checks if the server is ready to receive an application of any language)
│  │  │  |  ├─ Checking presence of git
│  │  │  |  ├─ Checking presence of language
│  │  │  |  ├─ Checking presence of package manager
│  │  │  |  ├─ Checking presence of required extensions
│  │  │  |  ├─ Your server is ready to deploy
│  │  |  ├─ Getting some informations about the server
│  │  |  ├─ Successfully setup "website" at "/home/forge/website"
│  │  ├─ CreateRelease (Creates a new release on the server)
│  │  │  ├─ CreateRelease/Clone (Clones a fresh instance of the repository by SCM)
│  │  │  |  ├─ Cloning repository in "/home/forge/website/releases/20160723125847"
│  │  │  |  ├─ Initializing submodules if any
│  │  ├─ PrepareRelease (Prepares the release for use)
│  │  |  ├─ Setting correct permissions on the files
│  │  |  ├─ Synchronizing shared folders
│  │  │  ├─ Dependencies (Installs or update the dependencies on server)
│  │  │  ├─ SwapSymlink (Swaps the symlink on the server)
│  │  │  |  ├─ Successfully deployed release 20160723125847
├─ Cleanup (Clean up old releases from the server)
|  ├─ No releases to prune from the server
```

As you can see Rocketeer uses a `tree`-like display to show you what is going on.

1. First it calls the Deploy task; the Deploy task, like the Check task that we saw earlier, has multiple possible strategies, the default one being the Rolling strategy since it uses symlinks to _roll over_ the releases without deleting them from the server.
2. The Deploy task then calls the `Primer` task: this is a task that is run before any deployment and if it does not succeed, deployment is halted. By default it does nothing but we can easily override it with our own logic. We'll see how to customize that right after
3. Then since this will be the first time you deploy to this server, `Setup` is called, which creates all the folders we'll deploy to
4. Next comes `CreateRelease` which basically just sets up a release for deployment – ie grab the code from wherever and put it in the right folder. By default the `Clone` strategy is used which gets the code from your repository of choice
5. This released is then prepared by `PrepareRelease` which install all the dependencies, shares folders, sets the right permissions on the files, and so on and so on
6. And finally `Cleanup` deletes any stale release from the server – by default only the last four releases are kept

## The Primer task

Let's first look at Primer. In order to execute checks before deployment, head over to the `hooks.php` file, you should see a `'tasks' => []` array – it allows you to register custom tasks with Rocketeer, so let's do so.

In this array you use the key to specify your task's name, in this case `Primer` (to override the core Primer task Rocketeer provides). Now, a task can be three thing:

- A string (`ls`) or array of strings (`['cd foo', 'ls']`)
- A closure (anonymous function)
- The name of a class

In this case let's use a closure:

**.rocketeer/config/hooks.php**

```php
<?php
return [
  'hooks' => [
    'tasks' => [
      'Primer' => function () {
        // Write our task here
      },
    ],
```

Now, imagine per example that we want it to check if we have any unstaged changes in Git before trying to deploy. We'd call `git status --porcelain` which would return changed files if present, or nothing if everything is committed.

So basically we want to do `!git status --porcelain`, let's write that. In Rocketeer, you run commands through the `run()` method. By default `run` runs command at the configured `root_directory` of the current connection (`cwd` in local) so you don't need to `cd` anywhere, you can just write:

**.rocketeer/config/hooks.php**

```php
<?php
'tasks' => [
    'Primer' => function () {
        return !$this->run('git status --porcelain');
    },
],
```

Here, `run` returns the output of the command, and since we want it to pass when there is _no output_ and fail where there is, we just return `!$this->run(...)`. If we run deploy again we indeed get a failure and deployment is halted:

```shell
$ rocketeer deploy
├─ Deploy (Deploys the website) [~1.8s]
│  ├─ Deploy/Rolling (Uses a system of folders current/releases/shared to roll releases)
│  │  ├─ Primer [~0.26s]
│  |  ├─ Project is not ready for deploy. You were almost fired.
```

## Configuring the server

Now that we're sure we can deploy, let's configure everything needed for the `Setup` task. Head over to the `remote.php` file. You should see a lot of options there but all of them are thoroughly explained so take your time to read through them.

### Shared files

One of the most important options is `shared`, it is a list of folders and files you want to share between all releases. Imagine your users can upload avatars and you store them into `public/avatars`, you would want to port over the contents of that folder between each deployment, correct? You can do so like this:

**.rocketeer/config/remote.php**

```php
<?php
'shared' => [
    'public/avatars',
],
```

Imagine you've deployed twice already, here is what the folders would look like:

```
├── current -> /home/www/website/releases/20160723145241
├── releases
│   ├── 20160723145241
│   │   └── public/avatars -> /home/www/website/shared/public/avatars
│   └── 20160723150812
│       └── public/avatars -> /home/www/website/shared/public/avatars
└── shared
    └── public
        └── avatars ==> contains the actual avatars
```

### Permissions

Another important aspect of most applications is setting permissions on the files. Imagine your application has a `logs` folder, or anything like that, it needs to be _web-writable_ (ie. accessible for writing by the application itself).

You can configure which folders and files to set as web-writable in the `permissions.files` option. Underneath it is a callback containing which commands will be run of each of those files/folder to make them web-writable. Since each server differs depending on what you use (apache, nginx, etc.) and what users you have, etc. this is fully configurable.

**.rocketeer/config/remote.php**

```php
<?php
'files' => [
  'public/avatars',
  'storage/logs',
],

// Per example imagine I just need to chmod the files
// on my server, I can do this:
'callback' => function ($file) {
  return 'chmod -R 755 '.$file;
},
```
