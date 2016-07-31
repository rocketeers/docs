# First contact

Now that Rocketeer is installed, first let's check that it works, type the following in your terminal:

```shell
$ rocketeer --version
```

If everything was installed correctly, you should get the latest version shown (3.x).

## Igniting Rocketeer

Ok, let's generate the files needed for Rocketeer to work, type `rocketeer ignite`. Rocketeer will ask you a series of questions. Take your time to read them carefully, and don't worry you can always change what you answered later on.

Rocketeer will do its best to guess the answer of each question, and display what it thinks is the correct answer in yellow between brackets, if you're ok with that, just type `Enter` to use that default answer.

![](http://i.imgur.com/6mollAs.gif)

> If this is your first time (or if you're following this tutorial), when Rocketeer asks you if you prefer classes or functions, answer `functions`. We'll go over the OOP setup later on but for a newcomer to Rocketeer, let's start with a minimal setup.

### The .env file

When ignition is done, Rocketeer will have created two things for you. First of all, it will have created a `.env` file in which your credentials will be stored. If you are not familiar with `.env` files you can read more [about it here](https://github.com/vlucas/phpdotenv#why-env) but to summarize it's a language-agnostic file in which to store global configuration securely.

If you are already using one for your application (Laravel, etc.) then we will just append to it.

This file should remain private, you do not track it into your repository of choice (Git, SVN, etc.) and as such if you have per example a `.gitignore` it is best to add that file to it to make sure it never ends up tracked somewhere with all your credentials public.

### The Rocketeer folder

#### Initial state

The second thing Rocketeer will have created is a `.rocketeer` folder, this is where the magic happens so let's take a look at it now:

```
$ tree .rocketeer
.rocketeer
├── app
│   ├── events.php
│   └── tasks.php
└── config
    ├── config.php
    ├── hooks.php
    ├── paths.php
    ├── plugins.php
    ├── remote.php
    ├── scm.php
    ├── stages.php
    └── strategies.php
```

It contains two things:

1. The first is a `config` folder where you can fine-tune Rocketeer however you need. Each option is explained thoroughly so take your time to go through all of them and update whatever needs updating. Don't worry too much about things you don't understand yet, we'll see each of them in review during this tutorial.
2. The second thing is a folder named `app` containing two files named `events` and `tasks` which will allow you to extend Rocketeer. In `functional` mode, any file in the `app` folder no matter their names or contents are loaded on boot, so while Rocketeer pre-creates these two files, you can create more than that, create subfolders, etc.

## Checking the configuration

The first you want to do upon ignition is opening up the main configuration file and check that your credentials are ok. There are two things that interest us: the default connection, and its configuration:

```php
<?php

// The default remote connection(s) to execute tasks on
'default' => ['production'],

'connections' => [
  'production' => [
    'host' => '%%PRODUCTION_HOST%%',
    'username' => '%%PRODUCTION_USERNAME%%',
    'password' => null,
    'key' => '%%PRODUCTION_KEY%%',
    'keyphrase' => '%%PRODUCTION_KEYPHRASE%%',
    'agent' => true,

    // The root directory where your applications will be deployed.
    // This path needs to start at the root, ie. start with a /
    'root_directory' => '%%PRODUCTION_ROOT%%',

    // The roles this server has, set to null if you do not use roles on your project
    'roles' => ['web', 'db'],

    // Options overrides for this server
    'config' => [],
  ],
],
```

So, `connections` is a dictionary of connections and their credentials. The key is the name of the connection. The top part is pretty straightforward, it's the base credentials. `agent` is whether to use agent forwarding if possible, ideally yes.

Now, `roles` is an array of roles you can assign to this connection. There is only one role Rocketeer uses internally, it's `db` to avoid running database-related tasks on connections that don't have one. You can define any role you'd like:

```php
'roles' => ['db', 'api'],
```

Then, `config` is an array of overrides. It's a replica of Rocketeer's configuration but scoped to this connection. If per example we had a `staging` server on which we wanted to use the `develop` branch of our repository we would do it like this:

```php
'config' => [
  'scm' => [
    'branch' => 'develop',
  ],
]
```

The key here (`scm`) is the name of the file it's in, so since `branch` is in `scm.php` we create a `'scm' => []` array within `config`.
