# SecRepo

## Transparent Git Repo Encryption
Encrypted files in the Git repo are transparently decrypted in your workspace using Git smudge/clean and text filters.

## Install
See platform-specific notes:
   * Linux.md
   * Cygwin.md
   * Windows.md

## git secrepo
The main interface to secrepo is via the `git-secrepo` command in your bin which is also accessed as `git secrepo`.
To see the available options and subcommands do:
   * `git secrepo -h`
   * `git secrepo subcmd -h`

## Basic Key Management
To add a first key to your repo, do:
   * `git secrepo --new default`

## Configuring Filters
Secrepo works by configuring filters in your git configuration. You also need to activate these filters in you git working tree in `.gitattributes`.

## Setting up .gitattibutes
http://git-scm.com/docs/gitattributes

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

```git secrepo -n```

At this point, you can use git log to see commits, diffs,
but files checked-out to your working tree will not be
decrypted. To edit an encrypted file, use the `sredit` command.

## Advanced Topics
   * Crypto.md
   * Advanced.md

