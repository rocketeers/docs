# Troubleshooting

### When I deploy my old scripts are running instead of the new, but the deployment was successful

PHP 5.5 has Opcache built-in. Opcache is supposed to invalidate when the script has changed, but it has issue when the script is in a symlinked folder and called from the webserver.

Basically, Opcache "thinks" that the script is `/var/www/hello/current/public/index` while in reality, it's `/var/www/hello/releases/20140101011/public/index`.

[Related issue](https://github.com/zendtech/ZendOptimizerPlus/issues/126#issue-18483771)
[Possible solution](https://github.com/zendtech/ZendOptimizerPlus/issues/126#issuecomment-24020445)