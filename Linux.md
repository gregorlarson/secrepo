# Linux Install
## Debian / Ubuntu
You will require packages git, python2.7 and python-crypto.
   * `apt-get install git python2.7 python-crypto`

I have not created a formal install or package and this point.

### Root Install

```sh
cd src
chmod 755 sr_* git-secrepo secrepo_test
cp * /usr/local/bin/
cd ../test/
secrepo_tests
```

### User Install
```sh
cd src
chmod 755 sr_* git-secrepo secrepo_test
cp * ~/bin/
cd ../test/
secrepo_tests
```
