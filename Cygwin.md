## SecRepo Install On Cygwin

Testing was done with Cygwin 32-bit, however, it should work with 64-bit.
You need to install:
   * `git` (of course)
   * `python` version 2.7.x
   * `python-crypto`

### Clone
```sh
git clone https://github.com/gregorlarson/secrepo.git
cd secrepo
```

### Install

```sh
cd src
chmod 755 sr* git-secrepo secrepo_tests
rm *.pyc
cp * /usr/local/bin/
cd ../test/
secrepo_tests
```

### Alternate install using symlinks
Useful if you want to be able to update/pull changes and have them be live automatically.
```sh
cd src
# Cygwin python 2.7 may not find modules symlinked into bin
# in the same way Linux or BSD does.
ln -s $PWD/secrepo.py /usr/lib/python2.7/site-packages/
rm *.pyc
for f in sr* git* secrepo_tests; do
   chmod 755 $f
   ln -s $PWD/$f /usr/local/bin/
done
cd ../test
secrepo_tests
```

## Cygwin Permissions

`chmod 0600` appears to map to something reasonable in NTFS security, so for now, that is how we protect `{git}/secrepo` and `~/.secrepo`.
  * If someone has a better idea, I would consider changes.
  * Note that Cygwin appears to work just fine with Truecrypt NTFS volumes as well as NTFS file
    encryption (Windows Professional required).

