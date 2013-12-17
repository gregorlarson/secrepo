## Windows Support

Secrepo has been tested on Windows-7 with Python27 64-bit.
Note that this is *not* the same as Cygwin support. That is a separate configuration. See Cygwin.md

The following notes apply to Windows native Git and Python.

### Install Git for Windows
You have probably already done this.
For reference I use this version which includes and msys bash environment with some basic *nix
utilities (vim, grep etc).  http://msysgit.github.io/

### Install Python for Windows
   * http://python.org/download/

### Install the PyCrypto libraries for Windows Python
The PyCrypto libraries can be downloaded in C and built if you have the tools, time and knowledge.
The homepage is at:
   * https://www.dlitz.net/software/pycrypto/

There are also some pre-built binaries available, however, you must download the binary version that matches your Windows Python install (above).
   * http://www.voidspace.org.uk/python/modules.shtml#pycrypto
   *

### Add Python to you PATH
Within the Git MSys shell environment, you can modify your path to include Python.
There are a number of ways to do this. An obvious way is to create a file `$HOME/.profile`
or `$HOME/.bash_profile` that modifies your path:

```sh
PATH="/c/Python27:$PATH"
```

### EDITOR Environment
The `sredit` command can use the `EDITOR` environment variable to invoke your favorite editor.
To use your own editor, `EDITOR` can refer to a windows .EXE, either directly or on your PATH.
`EDITOR` cannot refer to an msys shell script.

### Check Your Environment
* Open Git-Bash window
* `echo $PATH`
      > should include python

* `python`
     > should bring up a an interactive Python

* import Crypto
    > no error message
* ^Z to exit

### Install SecRepo
```sh
git clone https://github.com/gregorlarson/secrepo.git
cd secrepo/src
mv * ~/bin/
cd ../test
secrepo_tests
```

## Usage
From this point, the usage should be the same as Linux.
Refer to the README.md

## Windows Permissions
`os.chmod('filename',0600)` does not appear to do anything useful in Windows Python, so currently the .git/secrepo and ~/.secrepo files do not receive extra protection. These files contain your encryption keys.
   * I am open to suggestions here.
   * Depending on your environment, you could consider NTFS file encryption
     or a Truecrypt NTFS volume.
