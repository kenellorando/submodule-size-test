# submodule-size-test

## Purpose
This test is to check the size of repositories that utilize submodules.

## Questions
1. What size do submodules of a repository start with before they are initially updated?
2. When a submodule is removed, does the history of that submodule balloon the size of the main repository?

## Test Log
```
(starting with a fresh clone, where the submodule is present in the remote but is not yet updated locally)

kenel@JETBLACK MINGW64 ~/code/submodule-size-test (master)
$ du -h
... (cut)
65K     ./.git
0       ./static
71K     .

kenel@JETBLACK MINGW64 ~/code/submodule-size-test (master)
$ git submodule update --init --recursive
Submodule 'static' (https://github.com/kenellorando/cadence_static) registered for path 'static'
Cloning into 'C:/Users/kenel/code/submodule-size-test/static'...
Submodule path 'static': checked out 'eea27d4048a2c5a69b9eeffbd8b8791dbb59d3d1'

kenel@JETBLACK MINGW64 ~/code/submodule-size-test (master)
$ du -h
... (cut)
21M     ./.git/modules
22M     ./.git
21M     ./static
43M     .

kenel@JETBLACK MINGW64 ~/code/submodule-size-test (master)
$ git submodule deinit static
Cleared directory 'static'
Submodule 'static' (https://github.com/kenellorando/cadence_static) unregistered for path 'static'

kenel@JETBLACK MINGW64 ~/code/submodule-size-test (master)
$ du -h
... (cut)
21M     ./.git/modules
22M     ./.git
0       ./static
22M     .
```

`deinit` seems to remove the content itself but `.git` is still large in size.

At this point, I nuke the repo and reclone. This is to check whether future clones are ballooned in size after removal.

```
$  git clone https://github.com/kenellorando/submodule-size-test.git
Cloning into 'submodule-size-test'...
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 9 (delta 0), reused 6 (delta 0), pack-reused 0
Unpacking objects: 100% (9/9), done.

kenel@JETBLACK MINGW64 ~/code
$ cd submodule-size-test/

kenel@JETBLACK MINGW64 ~/code/submodule-size-test (master)
$ du -h
...
68K     ./.git
69K     .
```

The .git size is not polluted on a fresh clone. Thus the repository size will not be huge for future cloners if large objects are stored in a submodule. For those that did have the submodule and do not want to destroy the entire repository and start over, the best solution would appear to be to `rm -rf .git/modules/<module name>`.

## Results
1. What size do submodules of a repository start with before they are initially updated? - **Zero.**
2. When a submodule is removed, does the history of that submodule balloon the size of the main repository? - **No, not for new cloners. Those that previously had the submodule installed can run `rm -rf .git/modules/<module>` to remove it.**

