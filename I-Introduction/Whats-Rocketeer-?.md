Rocketeer is a fast and easy deploying tool for the PHP world. If you've already used *Capistrano* in the past you're already familiar with the gist of what it does, and can probably skip this section. The rest of you, bear with me.

Whether you've always deployed manually via FTP, plugins like SFTP for Sublime or via custom-made deploy scripts, Rocketeer is here to help you and automate your burden.
Please note that Rocketeer **requires an SSH connection** – meaning if you're on a shared hosting, I probably can't do anything for you.

## What it does

### Core folders

At its core, this package's strategy is inspired by Capistrano's and is relatively simple.
Before anything you'll provide Rocketeer with a `root_folder` on your server – this folder is Rocketeer's little self-contained world : whatever it does will be in that folder.

Then you give it your `application_name`. This is to let Rocketeer handle having multiple applications on the same server. The application name will be used to create a subfolder in the `root_folder` where everything related to _this_ application will happen.
If per example your `root_folder` is `/var/www/` and your `application_name` is `facebook`, then Rocketeer will create `/var/www/facebook/` and everything it does in the span of this project will happen in that folder.
Everything is packaged and contained, that way you can have as many applications on your server and it will still be all smooth.

### The strategy

As I said, Rocketeer's folder architecture is inspired by Capistrano's. Because as much as there are aspects of the latter I dislike, you cannot go against something that's been though out and refined for years either.

In the folder mentioned above (`/var/www/facebook/`), Rocketeer will create three folders.

**current** is where the _latest_ version of your application will always be. No matter what happens in the other folders, **that's the folder you want to serve online**. You never ever serve any other folder than this one, if you need to serve a release that is not the latest one (because the latest one is bugged per example) then you need to use Rocketeer's tools to rollback.
In our case, the Apache directive will look like this :

```apache
<Directory /var/www/facebook/current/public/>
  Options Indexes FollowSymLinks MultiViews
  Order allow,deny
  Allow from all
  AllowOverride All
</Directory>
```

The second folder, **releases**, is where the history of your application is stored. Every time you hit `deploy`, a timestamped folder will be created in _releases_ (20130721010101 per example for 2013-07-21 01:01:01). You can configure in your config file how deep the history goes : by default it will keep the four latest releases.
Once a new release is created and is ready to be served, Rocketeer will update the [symlink](http://en.wikipedia.org/wiki/Symbolic_link) of the `current` folder to make it point to it. This system is particularly flexible as it allows Rocketeer to simply update what folder `current` points to in case of rollback.

And finally the third folder, **shared**, is where files that are shared between each releases are stored. Take per example our Facebook application, it has users that can upload their avatars on it, and they are stored in `public/users/avatars`. This is all fine until you decide to deploy again and Rocketeer creates a new release pristine folder from scratch where your uploaded images won't be.
To solve this problem, in the config file you have a `shared` array where you can put paths relative to the root folder of your application, like `public/users/avatars`. Once Rocketeer see that, it will automatically move the folder `avatars` to `shared` and from there, every time you deploy, the new release will inherit all the shared folders.
By default Rocketeer always shares the logs of the application so that an history of the Exceptions that occurred is kept, but you can add as many folders as you like depending of your application. If you have an SQLite database that is stored in a file, you might want to share it too.

To sum it up, here is what your remote server will look like :

```
| var
|-- www
  |-- facebook
    |-- current => /var/www/facebook/releases/20130721000000
    |-- releases
    |  |-- 20130721000000
    |  |  |-- app
    |  |     |-- storage
    |  |       |-- logs => /var/www/facebook/shared/app/storage/logs
    |  | 20130602000000
    |-- shared
      |-- app
        |-- storage
          |-- logs
```