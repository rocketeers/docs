### Rocketeer\Server

	getSeparator()

Get the directory separators used on the remove server

	getLineEndings()

Get the remote line endings used on the remove server

	getValue($key, $fallback = null)

Get a value from the deployments file, or a `$fallback` value

	setValue($key, $value)

Set a value from the deployments file

	updateRepository($data)

Replace the contents of the deployments file with a `$data` array

	getRepository()

Get the contents of the deployments file

	deleteRepository()

Deletes the deployments file