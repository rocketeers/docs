# 証明書

<!--original
# Credentials
-->

デフォルトの状態では、Rocketeerは、サーバーやリポジトリの多くの情報を求めてきます。そして、設定フォルダをバージョントラックするときに、資格情報の素のテキストが見えていることに、あまりよい感じがしないかもしれません。
この問題を解決として、コマンドプロンプトは、見つからない資格情報があると訪ねてきて、それらを、ローカルマシンの隠しJSONファイルに保存します（デフォルトで`~/.rocketeer`）。これは、資格情報に関連する情報は、設定ファイルの中で空白にしておいて、Rocketeerが最初のデプロイの際にその値を聞いてきて、それらを保存し、再び煩わせられることはなく、デプロイ設定の資格情報をないままにできます。

<!--original
By default Rocketeer asks for a lot of informations on your server and repository, and if you track the configuration folder you may not be comfortable with storing the credentials in plain view.
To solve that problem, the command line will prompt you for any missing credential and store them in an hidden JSON file on your local machine (by default in `~/.rocketeer`). That means you can leave all credentials-related options empty and Rocketeer will simply ask the values to you on deploy the first time, save them, and never bother you with it again while leaving your deployment configuration credentials-free.
-->

これら（バイナリーのパス・資格情報などの）いくつもの聞かれてRocketeerのローカルに保存させる必要がある情報は、その保存したファイル自体は**決して**バージョントラックさせるべきではありません。

<!--original
That file should **never** be tracked, it's a local storage for Rocketeer to put the various informations it needs to ask you and remember them (binaries paths, credentials, etc).
-->


##保存した資格情報の消去

<!--original
## Flushing the credentials store
-->

Rocketeerに再び資格情報を聞かせる（打ち間違えをしたときなど）必要があるときには、`rocketeer flush`とだけ打てばよく、これによってプロジェクトに関連するローカルの保存された情報が消去されます。次に何かのコマンドを実行する際に消された資格情報が再び聞かれます。

<!--original
If you want Rocketeer to ask for your credentials again (in case you made a typo or something) all you need to do is run `rocketeer flush` which will flush the local storage related to that project. The next time you run any command it'll prompt you for any missing credentials again.-->
