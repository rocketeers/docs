# After the first deployment

Now once you've deployed a first time, here are some things you might want to do.

## Checking the status

If you run `rocketeer current`, it will tell you which releases are present on the server, at which date they were deployed, and if they failed to deploy or not:

```shell
$ rocketeer current
├─ Current (Display what the current release is)
|  ├─ The current release is 20160731105128 (3c68d735f418bdd13e5cb8cf7bd0e562395fad99 deployed at 2016-07-31 10:51:28)

Here are the available releases :

 --- ---------------- --------------------- --------
  #   Path             Deployed at           Status
 --- ---------------- --------------------- --------
  0   20160731105128   2016-07-31 10:51:28   ✓
 --- ---------------- --------------------- --------
```

## Rolling back cause you fucked up

If you just deployed and then notice that you fucked up and the currently online release is borked, you can run `rocketeer rollback` to go back to the previous release easily:

```shell
$ rocketeer rollback
├─ Rollback (Rollback to the previous release, or to a specific one)
|  ├─ Rolling back to release 20160731105128
```

You can also rollback to a _specific_ release through the `--list` flag:

```shell
$ rocketeer rollback --list
├─ Rollback (Rollback to the previous release, or to a specific one) [~2.48s]

Here are the available releases :

 --- ---------------- --------------------- --------
  #   Path             Deployed at           Status
 --- ---------------- --------------------- --------
  0   20160731105128   2016-07-31 10:51:28   ✓
  1   20160730105128   2016-07-30 10:51:28   ✓
 --- ---------------- --------------------- --------

 Which one do you want to go back to ? [0]:
 > 1

|  ├─ Rolling back to release 20160730105128
```

## Update the application quickly

If you make a small change to the application but don't want to do a full deploy just because you changed one line, you can use the `rocketeer update` command. It will update the _currently online_ release (basically do a `git pull` and update the dependencies).

## Remove all the things

If you don't want your application on your server anymore, you can use `rocketeer teardown` to delete all the files from it.

## Would you like to know more?

Rocketeer has a lot more to offer than what we just glossed over, you should now head over to the Guides section and learn about the topics that interest you. Good luck rocketeer!
