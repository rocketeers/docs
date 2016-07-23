# Setup

Installing Rocketeer on a project is quite easy, there are several ways to do so.

## Via the compiled archive

The easiest way is to just get the archive by doing downloading it [at the following address](http://rocketeer.autopergamene.eu/versions/rocketeer.phar) and putting it in your application's directory.

```shell
$ wget http://rocketeer.autopergamene.eu/versions/rocketeer.phar
$ chmod +x rocketeer.phar
```

You can also install it globally by executing the following command which will then give you a global `rocketeer` command to use in any folder.

```bash
$ mv rocketeer.phar /usr/local/bin/rocketeer
```

## With Composer

You can also install Rocketeer as a global Composer package like any other package. Don't forget to add `~/.composer/vendor/bin` to your `$PATH`!

```bash
$ composer global require anahkiasen/rocketeer
```
