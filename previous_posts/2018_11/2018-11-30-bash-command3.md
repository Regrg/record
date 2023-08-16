---
layout: post
---

#### find: find file under certain directory
- find [path] [options] [tests] [actions]
path: can be absolute path or relative path or multiple paths
options: `-depth` is to find files under directory then find directory itself. `-maxdepths N` is to find maximum N depths, etc.
tests: a judgement to filter files. True will pass. False will ignore. `-newer FILE` is that file existence is newer than FILE. `-type f` is that type of file is file. `-name PATTERN` is that file name complys regular expression PATTERN, etc
actions: `-print` to print out full name. `-ok command` is to execute command behind but need user to check before every line of comamnd executes. `-exec command` is to execute command behind. `Remember, commands need to end with \;`
	{} is a pair of special symbol. It will modify searched result with command behavior. Please see the last example below.

``` bash
find . -newer file1 -print: is to search file under current directory which is newer than test1 and print matched full file name.

find . -newer test2 -type f -printf: is to search file under current directory which is newer than tes2 and type is file then print matched full file name.

find . \( -name "_*" -or -newer test3 \) -type f -printf: is to search file under current directory which name is started with underline(regular expression) or is newer than test3, then check type is file, then finally print matched full file name.

find . -newer test4 -type f -exec ls -l {} \;: is to search file under current directory which is newer than test4 and type is file. Then execute command "ls -l" over the searched files and print result.
```

#### grep(General Regular Expression Parser): grep string inside files
- grep [options] PATTERN [FILES]
options: `-i` is to perform case-insensitive search, `-l` is to list only file name, `-h` is to hide file name, etc

