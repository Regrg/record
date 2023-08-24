---
layout: post
---

## File and Directory maintain

change file or directory permission
- `the command operator needs to have correspond permission to change file permission.` Ex file owner or super user

``` bash
#include <sys/stat.h>
int chmod(const char *path, mode_t mode);
	=> path: file wants to change permission
	=> mode: desired mode for the file
	=> RETURN: 0 if success, -1 if failed
```

change file or directory ownership
``` bash
#include <sys/types.h>
#include <unistd.h>

int chown(const char *path, uid_t owner, gid_t group);
	=> path: file wants to change ownership
	=> owner: new owner id
	=> group: new group id
	=> RETURN: 0 if success, -1 if failed
```

unlink() to remove file. link() to copy file. symlink() to create shortcut
- Operating System records link number to every file. `unlink()` remove 1 link from a file. `link()` add 1 link to a file(bash command is ln). `symlink()` just creates a shortcut to a file but `doesn't` add link to a file.
- `when link to a file is 0. Operating System will remove the file from hardisk`

``` bash
#include <unistd.h>

int unlink(const char *path);
	=> path: file path wants to delete
	=> RETURN: 0 if success, -1 if failed

	=> operator needs to have write and execute permission to call this function
int link(const char *path1, const char *path2);
	=> path1: file to-be link
	=> path2: file links to path1
	=> RETURN: 0 if success, -1 if failed
int symlink(const char *path1, const char *path2);
	=> path1: file to-be link
	=> path2: file makes shortcut to path1
	=> RETURN: 0 if success, -1 if failed
```

## Directory modify
`mkdir()` to add directory. `rmdir()` to remove directory. `chdir()` to change directory(in program). `getcwd()` to get current working directory file path.

``` bash
#include <sys/stat.h>
int mkdir(const char *path, mode_t mode);
	=> path: new directory path
	=> mode: desired directory permission
	=> RETURN: 0 if success, -1 if failed

#include <unistd.h>
int rmdir(const char *path);
	=> path: wanted to remove directory
	=> RETURN: 0 if success, -1 if failed

#include <unistd.h>
int chdir(const char *path);
	=> path: directory wants to change to
	=> RETUNR: 0 if success, -1 if failed

#include <unistd.h>
char *getcwd(char *buf, size_t size);
	=> buf: buffer to store current working directory
	=> size: size of buf
	=> RETURN: pointer to current working directory. or NULL if error

	=> if path name is longer than size, will produce ERANGE error
```

## directory stream
In `direct.h`, there is a `DIR *` stream pointer(like FILE\* pointer). This pointer is easy for programmer to deal with directory search, handle, etc.

``` bash
#include <sys/types.h>
#include <dirent.h>

DIR *opendir(const char *name);
	=> name: directory wants to open
	=> RETURN: a directory stream pointer to name, or null if failed

	=> DIR * has limitation system by system

struct dirent *readdir(DIR *dirp);
	=> dirp: directory pointer wants to read
	=> RETURN: struct dirent consists every items under directory

	=> if read end of directory, will return NUll but error will not be set. But if error, will return NULL and errno will be set
	=> if directory is being modifying, the readdir() cannot promise to read correct directory content

long int telldir(DIR *dirp);
	=> dirp: directory pointer wants to tell
	=> RETURN: current position of the directory stream pointer

# move current position of directory pointer to specific position
void seekdir(DIR *dirp, long int loc);
	=> dirp: target directory pointer
	=> loc: desired position to move pointer. Should retrieved by telldir()

int closedir(DIR *dirp);
	=> dirp: directory pointer wants to close
	=> RETUNR: 0 if success, -1 if failed
```

## Error handle

- when error occurs, system will return error code and set `errno` global variable indicating several errors status

``` bash
#include <errno.h>

extern int errno;
```

some error numbers of errno:

|name|description|
|-|-|
|EPERM|behavior not allowed|
|ENOENT|file or directory not exist|
|EINTR|system interrupt|
|EIO|IO error|
|EBUSY|device or resource busy|
|EEXIST|file already exists|
|EINVAL|invalid parameter|
|EMFILE|too many files opened|
|ENODEV|device not exists|
|EISDIR|is a directory|
|ENOTDIR|is not a directory|

translate error value to human readable string
``` bash
#include <string.h>

char *strerror(int errnum);
	=> errnum: desired error value
	=> RETURN: translated error string if success, truncated error message and ERNAGE will be set if string buffer is insufficient. Unknown error and EINVAL will be set if error number is not recognized
```

another method to show error
``` bash
#include <stdio.h>
void perror(const char *s);
	=> s: strings wants to put in front of error message
	
	=> this will directly translate current global errno variable to human readable string to stderr. Truncated error message and ERNAGE will be set if string buffer is insufficient. Unknown error and EINVAL will be set if error number is not recognized 
```

## Proc system information directory
- cpuinfo, meminfo can access CPU or memory information with enough permission
- there are many directories with number existed in /proc. Each number of directory represents the `PID` of a executing process. You can access the provess information with enough privilege. Ex: `cmd` is process current working directory. `cmdline` is command line information. `environ` is environment variables information. `fd` is file descriptors opened by process


## fcntl

control file descriptor with command
``` bash
#include <fcntl.h>

int fcntl(int fildes, int cmd);
	=> fildes: target file descriptor
	=> cmd: want-to-operate command
	=> RETURN: success inforamtion depends on command, or -1 if failed
int fcntl(int fildes, int cmd, long arg);
	=> fildes: target file descriptor
	=> cmd: want-to-operate command
	=> arg: argument depends on cmd
```

Examples of fcntl():
- fcntl(fildes, F_DUPFD, newfd): duplicate fildes and return a `new` file descriptor equal or bigger than newfd
- fcntl(fildes, F_GETFD): get file descriptor flags associated to fildes. Ex: FD_CLOEXEC indicates that file descriptor will be closed after exec() is called
- fcntl(fildes, F_SETFD, flags): set file descriptor flags associated to fildes. Ex: FD_CLOEXEC above
- fcntl(fildes, F_GETFL), fcntl(fildes, G_SETFL. flags): get or set file descriptor status flags. Ex: O_NONBLOCK, O_APPEND, etc

## mmap

``` bash
#include <sys/mman.h>
void *mmap(void *addr, size_t len, int prot, int flags, int fildes, off_t off);
	=> addr: 0 if dynamic allocated. or not 0 if want to choose specific address
	=> len: length of mapped memory
	=> prot: protections, region accessibility
	=> flags: data modified method
	=> fildes: want-to-mapped file descriptor
	=> off: offset in the file which memory will correspond to
	=> RETURN: a pointer points to mapped memory of file descriptor
```

prot can be: 

|name|description|
|-|-|
|PROT_READ|region can be read|
|PROT_WRITE|region can be write|
|PROT_EXEC|region can be execute|
|PROC_NONE|region cannot be access|

flags can be:

|name|description|
|-|-|
|MAP_PRIVATE|region is private, modification can only stay inside region|
|MAP_SHARED|region is shared, modification can affect into file's content|

## msync
copy the content of the mapped memory region into the file's content right away

``` bash
#include <sys/mman.h>

int msync(void *addr, size_t len, int flags);
	=> addr: memory mapped pointe
	=> len: length of the memory mapped region
	=> flags: control update method
	=> RETURN: 0 if success, -1 if failed
```

flags can be:

|name|description|
|-|-|
|MS_ASYNC|async write to file|
|MS_SYNC|sync write to file|
|MS_INVALIDATE|update latest data from file|

unmap mapped memory and release resouces
``` bash
#include <sys/mman.h>
int munmap(void *addr, size_t len);
	=> addr: mapped pointer wants to release
	=> len: length of the mapped region
	=> RETURN: 0 if success, -1 if failed
```
