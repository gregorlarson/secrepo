## SecRepo Install On Cygwin

Testing was done with Cygwin 32-bit, however, it should work with 64-bit.
You need to install:
   * `git` (of course)
   * `python` version 2.7.x
   * `python-crypto`


### Root Install

```sh
cd src
chmod 755 sr* git-secrepo secrepo_test
cp * /usr/local/bin/
cd ../test/
secrepo_tests
```

## Cygwin Permissions

`chmod 0600` appears to map to something reasonable in NTFS security, so for now, that is how we protect `{git}/secrepo` and `~/.secrepo`.
  * If someone has a better idea, I would consider changes.
  * Note that Cygwin appears to work just fine with Truecrypt NTFS volumes as well as NTFS file
    encryption (Windows Professional required).

