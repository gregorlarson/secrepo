# Linux Install
## Debian / Ubuntu
You will require packages git, python2.7 and python-crypto.
   * `apt-get install git python2.7 python-crypto`

I have not created a formal install or package and this point.


### Clone
```sh
git clone https://github.com/gregorlarson/secrepo.git
cd secrepo
```

### Root Install

```sh
cd src
chmod 755 sr* git-secrepo secrepo_tests
rm *.pyc
cp * /usr/local/bin/
cd ../test/
secrepo_tests
```

### User Install
```sh
cd src
chmod 755 sr* git-secrepo secrepo_tests
rm *.pyc
cp * ~/bin/
cd ../test/
secrepo_tests
```

### Development Install
Useful if you want to be able to update/pull changes and have them be live automatically.
```sh
cd src
chmod 755 sr* git-secrepo secrepo_tests
rm *.pyc
ln -s $PWD/* ~/bin/
cd ../test/
secrepo_tests
```
