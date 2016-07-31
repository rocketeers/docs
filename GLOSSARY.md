# GLOSSARY

## Closure

A closure is an anonymous function (ie. a function without name):

```php
function() {
  return $this->run('ls');
}
```

## Task

A task is something to be executed locally or on a server. It can be as basic as a simple `ls` to a complex flow with prompts and filesystem manipulations and such. A task can be written either as a string/array of strings, a closure or a class.

## Strategy

A strategy is a fictional task interface which can have multiple concrete implementations.

## Symlink

A symlink (or [symbolic link](http://en.wikipedia.org/wiki/Symbolic_link)) is a virtual file/folder that points to another actual file/folder, like a shortcut.

## VCS

A VCS (or Version Control System) is a software used to track and organize changes in your codebase, such as **Git**, **Mercurial**, **SVN**, etc.
