# Getting started with Git

## Link to our introductory course
[https://kirstiejane.github.io/friendly-github-intro/](https://kirstiejane.github.io/friendly-github-intro/)

## More advance documentation

- [Pro Git book by Scott Chacon and Ben Straub](https://git-scm.com/book/en/v2)
  - Jump to 'Private Small Team' section of [Chapter 5.2 Distributed Git - Contributing to a Project](https://git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project)

- use case: two developers working on a private shared repo
  1. The first developer, John, clones the repository, makes a change, and commits locally.
  ```bash
  git clone john@githost:repo.git
  git commit -am 'remove invalid default value'
  ```
  - The second developer, Jessica, does the same thing – clones the repository and commits a change
  ```bash
  git clone jessica@githost:repo.git
  git commit -am 'add reset task'
  git push origin master
  ```
  - John tries to push his change up, too.
  ```bash
  git push origin master
  error: failed to push some refs to 'john@githost:repo.git'
  ```
  John isn’t allowed to push because Jessica has pushed in the meantime. This is especially important to understand if you’re used to Subversion, because you’ll notice that the two developers didn’t edit the same file. Although Subversion automatically does such a merge on the server **if different files are edited, in Git you must merge the commits locally.** John has to fetch Jessica’s changes and merge them in before he will be allowed to push.
  ```bash
  git fetch origin
  git merge origin/master
  git push origin master
  ```

## Moving or renaming files

- [Moving a file to a new location using the command line](https://help.github.com/articles/moving-a-file-to-a-new-location-using-the-command-line/)
  ```bash
  mv /old-folder/image.png /new-folder/.
  git add /new-folder/image.png
  git commit -m "Move file to new directory"
  git push origin master
  ```
- [Renaming a file using the command line](https://help.github.com/articles/renaming-a-file-using-the-command-line/)
  ```bash
  git mv old_filename new_filename
  git commit -m "Rename file"
  git push origin master
  ```

  NB. This [post](http://stackoverflow.com/questions/2314652/is-it-possible-to-move-rename-files-in-git-and-maintain-their-history) says Git detects renames rather than persisting the operation with the commit, so whether you use `git mv` or just a plain `mv` doesn't matter.
