### Rocketeer\Rocketeer

	getOption($option)

Get an option from Rocketeer's config file

	setStage($stage)

Set the stage Tasks will execute on

	getStage()

Get the current stage

	getStages()

Get the various stages provided by the User

	getApplicationName()

Get the name of the application to deploy

	getShared()

Get an array of folders and files to share between releases

	usesSsh()

Whether the repository used is using SSH or HTTPS

	hasCredentials()

Whether credentials were provided by the User for the repository

	getRepository($username = null, $password = null)

Get the URL to the Git repository

	getRepositoryBranch()

Get the Git branch

	getFolder($folder = null)

Get the path to a folder, taking into account application name and stage

	getHomeFolder()

Get the path to the root folder of the application