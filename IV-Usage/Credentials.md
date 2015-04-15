# 証明書

<!--original
# Credentials
-->

ロケッティアのデフォルトは、サーバやレポジトリの情報を求めてきます。
設定フォルダ（configurationフォルダ）を追跡するとしたら、いい感じがしないかもしれないです。よく見える場所の証明書の格納？
問題を解くには、コマンドラインは、
ローカルのJSONファイルの中で、保存します。
と言うことは、証明書関連のオプションは空だということ。そして、わかりやすく最初にデプロイする時に、シンプルなバリューをきいてきて、保存します。
次回からは余計な手間はかけさせません。展開　構成　　証明書フリーで

<!--original
By default Rocketeer asks for a lot of informations on your server and repository, and if you track the configuration folder you may not be comfortable with storing the credentials in plain view.
To solve that problem, the command line will prompt you for any missing credential and store them in an hidden JSON file on your local machine (by default in `~/.rocketeer`). That means you can leave all credentials-related options empty and Rocketeer will simply ask the values to you on deploy the first time, save them, and never bother you with it again while leaving your deployment configuration credentials-free.
-->

決して追跡させるべきではないです。とっておく必要がある様々な情報は（バイナリのパス・証明書等々）は、ロケッティアのローカルにあります。

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
