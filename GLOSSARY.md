# GLOSSARY

## Closure

A closure is an anonymous function (ie. a function without name):

```php
function() {
  return $this->run('ls');
}
```

## Composer

A [package manager for PHP](https://getcomposer.org/doc/00-intro.md), leveraged by Rocketeer for plugins.

## Container

An [container](http://container.thephpleague.com/) is in spirit a big array of shared instances of classes. Rocketeer uses one internally as a service locator, for everything. So you can swap any of Rocketeer's classes at any time.

## Exit status

An exit status is an arbitrary number returned from a command to indicate if it worked or not. Common behavior is to return 0 for success and other numbers for specific errors/warnings.

## Tasks

A task is something to be executed locally or on a server. It can be as basic as a simple `ls` to a complex flow with prompts and filesystem manipulations and such. A task can be written either as a string/array of strings, a closure or a class.

## Service providers

A class that registers other classes into a container.

## Strategies

A strategy is a fictional task interface which can have multiple concrete implementations.

## Symlink

A symlink (or [symbolic link](http://en.wikipedia.org/wiki/Symbolic_link)) is a virtual file/folder that points to another actual file/folder, like a shortcut.

## VCS

A VCS (or Version Control System) is a software used to track and organize changes in your codebase, such as **Git**, **Mercurial**, **SVN**, etc.

## Web-writable

Accessible for writing by the application itself.
