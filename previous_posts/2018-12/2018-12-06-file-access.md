---
layout: post
---

#### file descriptor access
``` bash
#include <unistd.h>

size_t write(int fildes, const void *buf, size_t nbytes);
	fildes: file descriptor to write
	buf: prepared buf to write
	nbytes: number of byte wanted to write to file descriptor
	RETURN: number of byte written in file descriptor, -1 if error. Or < nbytes if some errors happened

size_t read(int fildes, void *buf, size_t nbytes);
	fildes: file descriptor to read
	buf: prpared buf to put read data
	nbytes: number of byte wanted to read from file descriptor
	RETURN: number of byte read from file descriptor, -1 if error. Or < nbyteps if some errors happened
```

open new file descriptor
``` bash
#include <fcntl.h>
# some UNIX systems need two imports below
#include <sys/types.h>
#include <sys/stat.h>
#

int open(const char *path, int oflags);
int open(const char *path, int oflags, mode_t mode);
	path: specified file name
	oflags: O_RDONLY, O_WRONLY, ORDWR, O_APPEND, O_TRUNC(remove original file content), O_CREAT(create file if not exist), O_EXCL(conbine with O_CREAT, make open be atomic, and prevent two processes create same file at a time), etc
	mode: if oflags contain O_CREAT, file permission will be decided here
	RETURN: created file descriptor, or -1 if error
```
##### the number of file a process can opened is defined in `limit.h` OPEN_MAX. POSIX defined the number must be bigger than 16


#### umask: a mask belongs to current user. The new file descriptor permission will be calculated with the mask `complement` to decide actual permission to the file
ex: if a user's mask is 0x001, and mode is S_IXOTH(others user have execute permission), the mask complement is 0x776, and the mode is 0x001. The others user won't have execution permission in the end because the execution permission was filtered by mask

#### close file descriptor: A VERY IMPORTANT STEP after using file descriptor. Some systems don't return write result during writing. They return result at close()
``` bash
#include <unsitd.h>

int close(int fildes);
	fildes: file descriptor wanted to close
	RETURN: 0 if success, -1 if failed
```


#### ioctl: IO control. Defined by each operating system. POSIX defined the interface of this function but please refer to device's manual to know how to use this function.

``` bash
#include <unistd.h>

int ioctl(int fildes, int cmd, ...);
	fildes: file descriptor to execute latter command
	cmd: command wanted to execute
	...: maybe is parameter of the previous command
	RETURN: defined by system, -1 if failed
```

#### commands to manage file

reposition read/wrtie file offset
``` bash
#include <unistd.h>
#include <sys/types.h>

off_t lseek(int fildes, off_t offset, int whence);
	fildes: file descriptor wanted to reposition
	offset: offset position compare to whence
	whence: SEEK_SET(set offset is an absolute position), SEEK_CUR(offset is relative to current position), SEEK_END(offset is relative to file end), etc
	RETURN: pointer of the desired position in file. -1 if error
```

get file status information.
- stat() and lstat() differ when the file is shortcut. lstat will get shortcut information, and stat will get the linked file information.
- file status information will be stored in stat structure. The structure can be different from each system but must contain:

|name|description|
|-|-|
|st_mode|file permission and file type|
|st_ino|inode of the file|
|st_dev|device inode|
|st_uid|user id|
|st_gid|group id|
|st_atime|last access time|
|st_ctime|last change time|
|st_mtime|last modify time|
|st_nlink|number of hard link to this file|

- st_mode can be:

|name|description|
|-|-|
|S_IFBLK|is block device|
|S_IFDIR|is directory|
|S_IFCHR|is character-oriented(read char-by-char) device|
|S_IFIFO|is FIFO(named pipe|
|S_IFREG|is regular file|
|S_IFLNK|is shortcut|
|S_ISUID|is set UID|
|S_ISGID|is set GID|

- some masks to verify st_mode:

|name|description|
|-|-|
|S_IFMT|is file format|
|S_IRWXU|get user read/write/execute permission|
|S_IRWXG|get group read/write/execute permission|
|S_IRWXO|get others read/wrtie/execute permission|
|S_ISBLK|is block file|
|S_ISCHR|is character-oriented file|
|S_ISDIR|is directory|
|S_ISFIFO|is FIFO|
|S_ISREG|is regulart file|
|S_ISKNK|is shortcut|

example using mask to check st_mode:
``` C
strcut stat statbuf;
mode_t modes;

stat("filename", &statbuf);
modes = statbuf.st_mode;

// file is directory and user has executing permission
if(!S_ISDIR(modes) && (modes & S_IRWXU) == S_IXUSR)
	...
```

``` bash
#include <unistd.h>
#include <sys/stat.h>
#include <sys/types.h>

int fstat(int fildes, struct stat *buf);
int stat(const char *path, struct stat *buf);
int lstat(const char *path, struct stat *buf);
```


