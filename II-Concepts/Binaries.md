[translation here]

<!--original
# Binaries
-->

[translation here]

<!--original
## Introduction
-->

[translation here]

<!--original
Rocketeer uses a variety of commands to execute your commands, most are low-level (change directory, symlink, etc) but it also needs to exploit the various tools your application uses to live, test, etc. For this it uses wrapper classes called Binaries.
-->

[translation here]

<!--original
### Using binaries
-->

[translation here]

<!--original
A binary class is any class extending `Rocketeer\Abstracts\AbstractBinary`. The core of said class is the `getCommand` method which allows you to build commands fluently, it takes three arguments: the command to call, its arguments, and its options.
-->

[translation here]

<!--original
Per example for the Git binary class:
-->

```php
$git->getCommand('clone', ['from', 'to'], ['--branch' => 'master']); // Yields `git clone from to --branch="master"
```

[translation here]

<!--original
You can also use the command as a method to have a more fluent interface. On that subject, options and arguments can be both passed as arrays and strings:
-->

```php
$git->clone(['from', 'to'], ['--branch' => 'master']); // Yields `git clone from to --branch="master"`

$git->clone('from to', '-b master') // Same result, you can pass array or strings interchangeably
```

[translation here]

<!--original
By default binary classes are only preoccupied with returning commands that are meant to be executed by a third party. They don't run anything anywhere, they just *build* what is meant to be executed and return it to you. Per example in the context of a class, this is how you'd create a git command and execute it:
-->

```php
$this->run(array(
  $git->clone(['from', 'to'], ['--branch' => 'master'])
));
```

[translation here]

<!--original
However binary classes also come with magic methods that allow you to directly execute the built command. For this, simply call any of the `run*` methods usually found on tasks, directly on the binary instance. This means these two examples do the same thing:
-->

```php
$this->runForCurrentRelease(array(
  $git->clone(['from', 'to'], ['--branch' => 'master'])
));

// And

$git->runForCurrentRelease('clone', ['from', 'to'], ['--branch' => 'master']);
```

[translation here]

<!--original
### Getting a binary instance
-->

[translation here]

<!--original
To get an instance of a binary in a task, or a strategy, simply use the `binary` method. It will return either an instance of `AnonymousBinary` if no built-in class for that binary exists, or if there already exists a class extending `AbstractBinary` in Rocketeer, it will return that:
-->

```php
$grunt = $this->binary('grunt'); // Instance of Rocketeer\Binaries\AnonymousBinary
$grunt->run('assets');

$artisan = $this->binary('artisan'); // Instance of Rocketeer\Binaries\Artisan
$artisan->run('seed');
```

[translation here]

<!--original
The difference is built-in binaries come with extra helper methods that are most of the time aliases to longer commands. Per example let's take a look at the `PHP::extensions` binary method which returns all available extensions of a PHP instance:
-->

```php
public function extensions()
{
	return $this->getCommand(null, null, ['-m' => null]);
}
```

[translation here]

<!--original
This allows you to do the following:
-->

```php
$extensions = $this->binary('php')->run('extensions');
$extensions = explode(PHP_EOL, $extensions);
```

[translation here]

<!--original
And get an array of available extensions.
-->

[translation here]

<!--original
Rocketeers comes with a small number of shortcut methods to get the built-in binaries:
-->

```php
$this->php() == $this->binary('php');
$this->artisan() == $this->binary('artisan');
// etc
```

[translation here]

<!--original
### Parent callers
-->

[translation here]

<!--original
Sometimes binaries have to be run by other binaries, per example the Artisan CLI tool needs to be run by PHP. For this you'll use the `setParent` method on the binary class. It accepts both instances of `AbstractBinary` or simple strings:
-->

```php
$artisan = $this->artisan();
$artisan->setParent('php');
$artisan->migrate(); // Returns `php artisan migrate`

// Or

$artisan = $this->artisan();
$artisan->setParent($this->php());
$artisan->migrate(); // Returns `/usr/bin/php artisan migrate` per example
```

[translation here]

<!--original
The advantage of using binary classes instead of running raw commands is that they have already built-in pathfinders. Meaning if you call `$this->artisan()->run('migrate')` instead of simply `$this->run('php artisan migrate')`, Rocketeer knows what those binaries are and where they are. You'll automatically get the correct path to PHP set as parent, the correct path to Artisan, etc.
-->

[translation here]

<!--original
## Creating your own binaries
-->

[translation here]

<!--original
Creating your own binaries is fairly straight-forward. At their core they're simply classes extending `Rocketeer\Abstracts\AbstractBinary`. There is no required method at all, they're just canvas to add your own aliases and/or known paths.
-->

[translation here]

<!--original
To set the predefined known paths of a binary, simply override the `getKnownPaths` method. It returns an array with two entries: first entry is what to call `which` on, and if that doesn't work, it'll use the second path. In other words, the second entry of the array is a fallback path.
-->

```php
<?php
namespace Acme;

use Rocketeer\Abstracts\AbstractBinary;

class Grunt extends AbstractBinary
{
	/**
	 * Get an array of default paths to look for
	 *
	 * @return string[]
	 */
	protected function getKnownPaths()
	{
		return array(
			'grunt',
			$this->releasesManager->getCurrentReleasePath('node_modules/.bin/grunt'),
		);
	}
}
```

[translation here]

<!--original
From there you're free to add whatever methods you feel are good to have around, and call them as any other commands:
-->


```php
<?php
namespace Acme;

use Rocketeer\Abstracts\AbstractBinary;

class Grunt extends AbstractBinary
{
	/**
	 * Get an array of default paths to look for
	 *
	 * @return string[]
	 */
	protected function getKnownPaths()
	{
		return array(
			'grunt',
			$this->releasesManager->getCurrentReleasePath('node_modules/.bin/grunt'),
		);
	}

	public function forceAssets()
	{
		return $this->assets(null, '--force');
	}
}
```

```php
$grunt = $this->binary('Acme\Grunt');
$grunt->runForCurrentRelease('forceAssets');
```
