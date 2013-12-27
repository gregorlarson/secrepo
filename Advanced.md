# Advanced SecRepo

## Using Global Key-store
When using the --global (-g) flag you effect keys stored in `~/.secrepo` rather than `{GIT_DIR}/secrepo`. The default local git repository will override the global default key. When decrypting, keys are available from the local git repo, global config or environment.

## Migrating History
   * Using git-secrepo with git-rebase
   * Using git-secrepo with git-filter-branch

### Warning on re-writing Git History
If you have never used filter-branch before, please be aware that this is a reasonably complex scenario and it is possible to make mess if you are not experienced with Git. I am working on the following assumptions:
 1. I am not providing a Git instruction manual. This is a fairly narrow discussion focus on specific scenarios.
 1. I assume you are not experimenting on your *master* repo instance. I assume you are doing these type of changes on a _disposable_ working tree so you can remove and re-clone if you don't like the results.
 1. The final renaming and deployment of branches is beyond the scope of this documentation. There are a number of problems you will encounter if you re-write history or maintain parallel branches.
 
### Migrating Non-Encrypted to Encrypted History
Create your `.gitattributes` file and copy to a temp file (/tmp/ga)'. The first history re-write is to insert the new `.gitattributes` file.
```sh
# config --encrypt seems to perform a bit better than config --all
git secrepo config -e
# Note that the following filters can take hours to run on a large repo with a lot of history.
#
git filter-branch -f --tree-filter 'cp -f /tmp/ga .gitattributes'
git filter-branch -f --tree-filter 'git-ls-files | git-secrepo encrypt'
git ls-files | git secrepo decrypt
git secrepo config -a
rm .git/index
git reset
```
If you have multiple gitattributes files you might have to repeat the first filter a few times:
```sh
git filter-branch -f --tree-filter 'test -d other_dir && cp -f /tmp/ga_other other_dir/.gitattributes'
```
### Migrating Encrypted to Non-Encrypted History
```sh
git secrepo config -u
git filter-branch -f --tree-filter 'git-ls-files | git-secrepo decrypt'
```
### Rewrite History to Change Keys

## Parallel Branches

### Maintaining a Parallel Non-Encrypted Branch

### Maintaining a Parallel Encrypted Branch

## Key History Analysis
   * git secrepo log
   
