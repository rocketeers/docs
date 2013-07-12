### Rocketeer\ReleasesManager

	getReleases()

Get all the releases on the server as an array

	getDeprecatedReleases()

Get an array of deprecated releases

	getReleasesPath()

Get the path to the releases folder

	getPathToRelease($release)

Get the path to a release

	getCurrentReleasePath()

Get the path to the current release

	getCurrentRelease()

Get the current release

	getPreviousRelease($release = null)

Get the release before `$release`, or before the current one if `null`

	updateCurrentRelease($release)

Update the current release to `$release`