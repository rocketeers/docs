### Rocketeer\Traits\Task

	getSlug()

Get the basic name of the Task

	getDescription()

Get what the Task does

	isSetup()

Check if the remote server is setup

	usesStages()

Check if the Task uses stages

	runForCurrentRelease($tasks)

Run actions in the current release's folder

	executeTask($task)

Execute another Task by name

	cloneRepository()

Clone the repo into a release folder

	updateRepository($reset = true)

Update the current release, `$reset` will reset unstaged changes beforehand

	updateSymlink($release = null)

Update the current symlink to a `$release`, latest release if `null`

	share($file)

Share a file or folder between releases

	setPermissions($folder)

Set a folder as web-writable

	runComposer()

Run Composer on the folder

	getComposer()

Get the path to Composer binary

	runMigrations($seed = false)

Run any outstanding migrations, `$seed` too if true

	runTests($arguments = null)

Run the application's tests, with facultative additional `$arguments`