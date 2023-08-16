---
layout: post
---

- chmod can change file or directory permission by setting 4(read), 2(write), 1(execute). Normally, we use three digits to set permission of `User`, `Group`, and `Others`. Ex: chmod 777 test.c. But there is a fourth digit representing special permission. Explain below:

- setuid bit: representing `4` of the fourth digit. This bit cannot set to directory, and only take effect if executable bit is set. If this bit is set, the file will always execute with root privilege, which means that it can be dangerous if not use this bit correctly.

#### or "chmod u+s test.c" to set setuid bit

the s in owner permission representing setuid bit:

`-rwsr-xr-x. 1 root root 27768 Feb 11  2018 /bin/crontab`


- setgid bit: `2` of the fourth digit. This bit can set to both file and directory, and, the same, only take effect if executable bit is set. If this bit is set to a file, instead of running with the privilege of the group of the user who started it, runs with the group which owns the file.
If this bit set to a directory, the same rule above will apply to all files under the directory. This feature to a directory is commonly used under sharing environment. People in the same group can all share files under the directory.


#### or "chmod g+s file" to set setgid bit

the s in the group permission representing setgid bit:

`drwxrwsr-x. 2 egdoc egdoc 4096 Nov  1 17:25 test`

- sticky bit: `1` of the fourth digit. This bit can only be apply to directory and cannot used on a file. If the sticky bit is set to a directory, all files under the directory can only modified by their owners. A example is /tmp directory under UNIX system. All user can add files under /tmp but only the onwer of the file can delete that file.

#### or "chmod o+t file" to set sticky bit

the T in the others permission representing sitcky bit, (The lower-case t will be shown if executable permission was set to that file):

`drwxrwxrwt. 14 root root 300 Nov  1 16:48 /tmp`
