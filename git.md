

# git tips

## Generate SSH key

    ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

## Test you ssh key

    ssh -vT git@hostname.com -p 4999

## Set your username with the following command:

    git config --global user.name "Billy Everyteen"

## Set your email address with the following command:

    git config --global user.email "your_email@example.com"

## create patch of what is in working directory

    git diff > /tmp/mypatch.patch

## apply patch onto working directory

    git apply /tmp/test/wkit2.patch

## more advanced patch

be on the latest commit
you want to patch from master to HEAD

    git format-patch master
  or
    git format-patch master --stdout > filename.patch

apply patch:

    git apply --check filename.patch
    git apply --stat filename.patch
    git am --signoff < filename.patch

## apply patch which do not match

    git am --ignore-space-change < test.patch

## Useful Aliases

### pimp your history display

    git config --global alias.show-graph "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr - %an)%Creset' --abbrev-commit --date=relative --all"

### Alias to display aliases

```
    git config --global alias.alias "config --get-regexp 'alias.*'"
```

## add an ssh remote

    git remote add origin ssh://user@host:1234/srv/git/example

## add an ssh remote

    git remote set-url origin https://github.com/USERNAME/REPOSITORY.git

## How to disable TLS/SSL verification for a single git command

    git -c http.sslVerify=false clone https://example.com/path/to/git


## Viewing all `git diffs` with vimdiff

    git config --global diff.tool vimdiff
    git config --global difftool.prompt false
    git config --global alias.d difftool

Typing `git d` yields the expected behavior, type `:wq` in vim cycles to the next file in the changeset.

## How to display changed files on a commit

    git show --pretty="format:" --name-only bd61ad98

    git diff-tree --name-only -r <commit-ish>

    git ls-tree --name-only -r <commit-ish>

## SVN git stuff

### Import Straight from the SVN repo

First, we need to fetch a copy of the repository.

    git svn clone -s -r 40000:HEAD https://svn.parrot.org/parrot # choose some recent-ish commit

note: it is assumed /trunk at the end of the url because of the -s parameter.

## After you've made however many commits, you can push up to the svn server with:

    git svn dcommit

### That will also bring your local tree up to date. To bring your tree up to date in general, run:

    git svn rebase

This will update your local checkout and then re-apply your local un-submitted commits on top of the new trunk.

### update your local repo history with new commits coming from the SVN server

    git svn fetch

### How to display svn revision in your git/svn repo?

    git log -z | tr '\n\0' ' \n' | sed 's/\(commit \S*\) .*git-svn-id: svn:[^@]*@\([0-9]*\) .*/\1 r\2/'

## good website on GIT

    http://www-cs-students.stanford.edu/~blynn//gitmagic/ch07.html

    http://trac.parrot.org/parrot/wiki/git-svn-tutorial

## How to start a git daemon

    git daemon --verbose --base-path=/cygdrive/d/java/


## How to get a file from a specific version

for a list of files

    git show <treeish>

for a file in particular

    git show <treeish>:<file>

So let’s say we want to go back four commits from our current HEAD, and we want the index.html file.

    git show HEAD~4:index.html

or

    git checkout 08618129e66127921fbfcbc205a06153c92622fe path/to/file.txt


## How to push a new local branch to remote repo and track it too

    $ git checkout -b mynewfeature
    ... edit files, add and commit ...
    $ git push -u origin mynewfeature


## How to remove submodules

* mv subfolder asubmodule_tmp
* git submodule deinit asubmodule    
* git rm asubmodule
* mv asubmodule_tmp asubmodule
* cd asubmodule
* rm .git .gitignore .idea build asubmodule.iml target
* git add asubmodule
* git commit -m "removed asubmodule module"

## How do I make Git ignore file mode (chmod) changes?

    git config core.fileMode false

## Undo part of unstaged changes in git?

    git stash -p; git reset --hard; git stash pop

## How to push tags

```bash
git tag R_2017-10-24
git push origin R_2017-10-24
```

## How do I make Git ignore file mode (chmod) changes?

    git config core.fileMode false

From git-config(1):

core.fileMode
       If false, the executable bit differences between the index and the
       working copy are ignored; useful on broken filesystems like FAT.
       See git-update-index(1). True by default.
The -c flag can be used to set this option for one-off commands:

    git -c core.fileMode=false diff
And the --global flag will make it be the default behavior for the logged in user.

    git config --global core.fileMode false
Warning
core.fileMode is not the best practice and should be used carefully. This setting only covers the executable bit of mode and never the read/write bits. In many cases you think you need this setting because you did something like chmod -R 777, making all your files executable. But in most projects most files don't need and should not be executable for security reasons.

The proper way to solve this kind of situation is to handle folder and file permission separately, with something like:

```
find . -type d -exec chmod a+rwx {} \; # Make folders traversable and read/write
find . -type f -exec chmod a+rw {} \;  # Make files read/write
```
If you do that, you'll never need to use core.fileMode, except in very rare environment.

Ref: https://stackoverflow.com/questions/1580596/how-do-i-make-git-ignore-file-mode-chmod-changes
