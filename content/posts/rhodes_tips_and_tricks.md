---
title: Rhodes tips & tricks
date: 2013-12-30
author: Pietro F. Maggi
summary: Collection of rhodes tips and tricks. More for the people that manage rhodes than who's only looking for info on how to write rhodes apps.
categories:
    - "Rhodes"
    - "RhoConnect"
    - "RhoMobile"
---

## How to rebuild the rhodes gem

```bash
git clone https://github.com/rhomobile/rhodes.git
cd rhodes
git checkout 3-5-stable
```

make and commit changes if needed, then:

```bash
rm -rf *
git reset --hard
git pull
```

Otherwise directly

```bash
rake gem
```

Copy files where you need it (on a different machine)
`gem install -l rhodes-3.5.1.13.2` (version given as an example)


## Rhodes Database encryption

Rhodes uses SQLite as database engine on all the supported platforms and, to provide encryption support we use different solutions depending by the target platform.

For Windows Mobile we use the [WinCrypt Library]()
The actual implementation is [under platform folder on github](https://github.com/rhomobile/rhodes/blob/master/platform/wm/rhodes/rho/common/RhoCryptImpl.cpp)

[Microsoft WinCrypt library support multiple algorithms](http://msdn.microsoft.com/en-us/library/aa923618.aspx) and we use RC4 in Windows Mobile.

For other operative systems we use the [SQLCipher plugin](http://sqlcipher.net/). A look at the [crypto.c source file included in the SQLite folder can give more info](https://github.com/rhomobile/rhodes/blob/master/platform/shared/sqlite/crypto.c).

## Making a pull request to rhodes...
First of all you need to fork rhodes:

Then you need to be sure that your fork is synced with upstream (this needs to be done before starting making changes), the following guide from GitHub came handy:

- [Syncing a fork](https://help.github.com/articles/syncing-a-fork)
- [How to use Pull Requests](https://help.github.com/articles/using-pull-requests)
