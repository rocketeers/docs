[translation here]

<!--original
# Troubleshooting
-->

[translation here]

<!--original
#### When I deploy my old scripts are running instead of the new, but the deployment was successful
-->

[translation here]

<!--original
PHP 5.5 has Opcache built-in. Opcache is supposed to invalidate when the script has changed, but it has issue when the script is in a symlinked folder and called from the webserver.
-->

[translation here]

<!--original
Basically, Opcache "thinks" that the script is `/var/www/hello/current/public/index` while in reality, it's `/var/www/hello/releases/20140101011/public/index`.
-->

[translation here]

<!--original
To solve the issue, add the following command to be run at the end of your deployment:
-->

```
php -r "opcache_reset();"
```

[translation here]

<!--original
**Note: if you are using PHP-FPM with nginx, the above will only flush the CLI cache, not FPM's**
-->

[translation here]

<!--original
To fix the problem in this case, add the lines below to your nginx server configuration. 
```
fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
fastcgi_param DOCUMENT_ROOT $realpath_root;
```
Usually the file is located at `/etc/nginx/fastcgi.conf`.
-->
