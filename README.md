# SecRepo
## Transparent Git Repo Encryption
Encrypted files in the Git repo are transparently decrypted in your workspace using Git smudge/clean and text filters.

## Safe Usage Warning
This script is still fairly new, which means there is some new-adoption risk. Any feedback or ideas on usability is welcome, thanks!

### Crypto Warning
Like any high-strength crypto tool, you need to be aware that if you loose your key, your data *cannot* be recovered. The developer does not have any special back-door or administrative access.

## Why would I use SecRepo?
Git is a great tool for tracking changes and synchronizing multiple work-spaces, however, to really take advantage of git you may want to have a _bare_ repo in the cloud, that you can access from any-where. Github is one example of this. You could also keep your bare repo on a small server on AWS or another cloud provider (this an approach I use). They problem is, you may have some sensitive files in your repo and cloud data is never completely under your control. Also, you might want to publish the contents of your repo, but keep a few files private due to an NDA or because they include name/contact details you do not have permission to publish. You may want a client or contractor to be able to access a repo but still keep some files private.

SecRepo is designed to mitigate these problems by allowing your bare repo to contain encrypted data that is transparently decrypted in *your* workspace, provided you have the correct keys installed. SecRepo does not prevent someone without the keys from cloning the repo, however, the encrypted files will not be decrypted without the correct keys. Only the encrypted file(s) will in that workspace.

## Install
See platform-specific notes:
   * Linux.md
   * Cygwin.md
   * Windows.md

## git secrepo
The main interface to secrepo is via the `git-secrepo` command in your bin which is also accessed as `git secrepo`.
To see the available options and sub-commands do:
   * `git secrepo -h`
   * `git secrepo {command} -h`

## Key Management and Security
SecRepo will retrieve keys from:
   * the local git repo, file {GIT_DIR}/srconfig
   * a global config name .srconfig in your home directory
   * environment variables
   * in a future version, I may allow keys to be retrieved from an independent keyring which is somewhat safer than a file on disk. For example:
      * gnome keyring
      * Linux kernel key retention service https://www.kernel.org/doc/Documentation/security/keys.txt
      * Other ideas for safer key storage? Let me know.

### Basic Key Management
To add a first key to your repo use: `git secrepo new default`
To list the keys use: `git secrepo keys`

### Key Security
It is *not* intended that secrepo provides the same level of security as a password vault or encrypted volume where the decryption keys are memorized by the user never stored on disk.
SecRepo encryption is really only as secure as the machine where you use the keys and keep the decrypted working tree.
Your SecRepo keys *are* stored on disk. This is necessary so that git is usable without frequent password prompts.

The srconfig files where your keys are stored are protected, however, an administrative user on your machine *will* be able to access your keys, also, your keys may be retrievable from a regular filesystem backup. If someone steals your keys, and they have access to the encrypted (bare) repo, they may be able to access the contents of your repo for an extended period, without your knowledge (including new commits) until your change your keys.

#### Stonger Security Ideas
If you are putting really sensitive information into a Git repo, you probably want to keep your working tree (checked out Git files) and SecRepo keys on an encrypted volume such as Truecrypt or LUKS or NTFS encryption. You also would want to control access to your bare repo (in the cloud) as must as possible (only allow ssh access and create separate keys for each legitimate user).  You probably want to change the SecRepo encryption key after revoking access for a user.

## Configuring Filters
Secrepo works by configuring filters in your git configuration. You also need to activate these filters in you git working tree in `.gitattributes`. A sub-command `git secrepo config` handles git configurations.
   * `git secrepo config -a` enables all the filters
   * `git secrepo config -n` enables only the textconv filters. Use this when working without keys (in an encrypted working tree).
   * `git secrepo config -u` disables the secrepo filters.

There are a couple non-standard configurations that I plan to use for maintaining separate encrypted / decrypted branches.  These configurations are sometimes what you want if you are re-writing history using a tree-filter in order to create a decrypted or encrypted branch.
   * `git secrepo config -e` enables encryption but not decryption.
   * `git secrepo config -d` enables decryption but not encryption.

To check your current configuration use: `git secrepo`

## Setting up .gitattibutes
   * ref: http://git-scm.com/docs/gitattributes

You can indicate which files are to be handled by secrepo. For example:
```awk
# Encrypt certificates
*.pem filter=private diff=private
```

To encrypted all files, indicate * then exclude a few in order for git to work:
```awk
# Encrypt all files
* filter=private diff=private

# Except for a few.
# Don't encrypt files git uses.
.git* filter= diff=

# Provide a note on encrypted repo.
README.DECRYPT filter= diff=
```

Currently, there is an issue with binary files (after decryption) because of the diff attribute, so if you repo contains a lot of binary files, you may want to
change how you use the diff parameter, to either include only the non-binary files, or, exclude the binary files. For example:
```awk
# Exclude some binary files from diff
*.jpg diff=
*.png diff=
*.psafe3 diff=
*.gpg diff=
*.p12 diff=
```
Or use the diff filter only on files you know are text and provide a useful diff.
```awk
* filter=private
*.txt diff=private
*.sh diff=private
Makefile diff=private
README diff=private
```

## Encrypted Files in Working Tree
Depending on the order you do things, you may end up with some encrypted files in your local working tree, when you really wanted them un-encrypted.
   * the necessary keys were not available when you checked-out.
   * the necessary filters were not configured when you checked-out.

## git secrepo decrypt
This decrypt sub-command allows you to decrypt files in your working-tree in-place. It will not disturb files that are already decrypted. If you have been using `sredit` then this is the method you need to use in order to preserve your changes (not a common scenario).
The secrepro decrypt command reads a list of file names from standard input and inspects each file to see if it can be decrypted. At the end of the process, it prints a summary of what was done. It is intended to be used in a pipe-line with `git ls-files` as follows:
```sh
git ls-files | git secrepo decrypt
```

### Dealing with the Index
When you change keys, .gitattributes or clean/smudge filters you may create an inconsistency between the work tree and the git index.

#### Removing the Index
To resolve inconsistencies in the git index, you may need to delete your `.git/index`, however, this will not cause encrypted files in your working tree to be decrypted. Also, the index may contain added or stashed file versions which will be lost if you delete the index.

This approach is also mentioned in http://git-scm.com/docs/gitattributes

#### Using `git reset`
If you do not have any changes in the local working tree and you to not have any changes stashed in the git index, then you can just delete your git index and use `git reset --hard` to overwrite your working tree. This *will* check-out decrypted versions of your files if the correct keys and filters are in place.

### Other


## Working in Encrypted Tree
It is possible to work in an encrypted work-tree where
some or all of the files are checked-out encrypted.
In this case, you will want to use only the text filter and
not the clean/smudge filters. This is done as follows:

```git secrepo config -n```

At this point, you can use git log to see commits, diffs,
but files checked-out to your working tree will not be
decrypted. To edit an encrypted file, use the `sredit` command.

## Advanced Topics
   * Crypto.md
   * Advanced.md

