# トラブルシューティング

<!--original
# Troubleshooting
-->

#### デプロイを実行したときデプロイは成功しているのに、新しいスクリプトではなく、古いスクリプトが稼働しています。

<!--original
#### When I deploy my old scripts are running instead of the new, but the deployment was successful
-->

PHP5.5は、ビルトインでOPcacheをもっています。OPcacheはスクリプトが変更された場合には破棄されるはずですが、スクリプトがシンボリックリンクされたフォルダーでWEBサーバーから参照されている場合には問題があります。

<!--original
PHP 5.5 has Opcache built-in. Opcache is supposed to invalidate when the script has changed, but it has issue when the script is in a symlinked folder and called from the webserver.
-->

基本的に、OPcacheは、`/var/www/hello/current/public/index`を実在のスクリプトであると"認識"します。実際には `/var/www/hello/releases/20140101011/public/index`であるのにです。

<!--original
Basically, Opcache "thinks" that the script is `/var/www/hello/current/public/index` while in reality, it's `/var/www/hello/releases/20140101011/public/index`.
-->

この問題を解決するには、デプロイの後に、以下のコマンドを追加します。

<!--original
To solve the issue, add the following command to be run at the end of your deployment:
-->

```
php -r "opcache_reset();"
```

**注意: もし、PHP-FPMとnginxを使っている場合には、上記コマンドは、CLIのキャッシュを削除するのみに、FPMのものは削除しません。**

<!--original
**Note: if you are using PHP-FPM with nginx, the above will only flush the CLI cache, not FPM's**
-->

この場合に問題を修正するには、以下の行をnginxサーバーの設定ファイルに加えます。

<!--original
To fix the problem in this case, add the lines below to your nginx server configuration. 
-->
```
fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
fastcgi_param DOCUMENT_ROOT $realpath_root;
```

通常、このファイルは、`/etc/nginx/fastcgi.conf`にあります。

<!--original
Usually the file is located at `/etc/nginx/fastcgi.conf`.
-->
