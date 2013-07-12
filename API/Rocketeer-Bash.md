### Rocketeer\Bash

	run($commands, $silent = false, $array = false)

Run actions on the remote server and gather the ouput.

- `$silent` will make the command(s) silent no matter what.
- `$array` will return the output as an array.

```
public function runInFolder($folder = null, $tasks = array())
```

Run commands `$tasks` in `$folder`

	which($binary, $fallback = null)

Get the path to a binary from the `which` command, or returns a `$fallback` path

	checkStatus($error, $output = null, $success = null)

Check the status of the last run command. If the command failed, display the `$error` message and print the `$output`.
If it succeeded, display the `$success` message and return the `$output`.

	symlink($folder, $symlink)

Symlinks two folders

	move($from, $to)

Move a file from `$from` to `$to`

	listContents($directory)

Get the contents of `$directory`

	fileExists($file)

Check if a file (or folder) exists

	createFolder($folder = null, $recursive = false)

Create a folder in the application's folder. `$recursive` allow the creation of parent folders if they don't exist.

	removeFolder($folder = null)

Remove a folder in the application's folder

	runRemoteCommands($commands, $array = false)

Run a raw command, without any processing, and return it's output as string or as an array if `$array` is `true`

	protected function getOption($option)

Get an option from the Command

	protected function processCommands($commands)

Process an array of commands. This will :

- Replace directory separators by the ones used on the server
- Execute Artisan commands in the correct environment if stages are used