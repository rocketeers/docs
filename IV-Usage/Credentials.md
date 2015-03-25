# 証明書

<!--original
# Credentials
-->

[translation here]

<!--original
By default Rocketeer asks for a lot of informations on your server and repository, and if you track the configuration folder you may not be comfortable with storing the credentials in plain view.
To solve that problem, the command line will prompt you for any missing credential and store them in an hidden JSON file on your local machine (by default in `~/.rocketeer`). That means you can leave all credentials-related options empty and Rocketeer will simply ask the values to you on deploy the first time, save them, and never bother you with it again while leaving your deployment configuration credentials-free.
-->

[translation here]

<!--original
That file should **never** be tracked, it's a local storage for Rocketeer to put the various informations it needs to ask you and remember them (binaries paths, credentials, etc).
-->


[translation here]

<!--original
## Flushing the credentials store
-->

[translation here]

<!--original
If you want Rocketeer to ask for your credentials again (in case you made a typo or something) all you need to do is run `rocketeer flush` which will flush the local storage related to that project. The next time you run any command it'll prompt you for any missing credentials again.-->
