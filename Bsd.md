## Testing in FreeBSD 10.0-RC3 (i386)

## Environment
```sh
pkg install git
pkg install python
pkg install py27-pycrypto
```

## Clone
```sh
git clone https://github.com/gregorlarson/secrepo.git
cd secrepo
```

## Root Install
```sh
cd src
rm *.pyc
cp * /usr/local/bin/
cd ../test
secrepo_tests
```

## User Install
```sh
cd src
rm *.pyc
cp * ~/bin/
cd ../test
secrepo_tests
```

## Development Install
Useful if you want to be able to update/pull changes and have them be live automatically.
```sh
cd src
rm *.pyc
ln -s $PWD/* ~/bin/
cd ../test
secrepo_tests
```
