---
layout: post
---

## Resource and Limitation

some consts of system limitation:

|name|description|
|-|-|
|CHAR_BIT|bit number of char|
|CHAR_MAX|max value of char|
|INT_MAX|max value of integer|
|NAME_MAX|max length of file name in char|

`NAME_MAX if the limitation of file system. Should use pathconf() to get the limitation for better system portability`

``` bash
#include <unistd.h>

long fpathconf(int fildes, int name);
	=> fildes: an open file descriptor
	=> name: name argument specifies the system variable to be queried. Ex: _PC_NAME_MAX, _PC_PATH_MAX, etc. Refer to man for more information
	=> RETURN: return current variable value if success, -1 but not set errno if does not have a limit in the system. -1 and set errno if error

long pathconf(const char *path, int name);
	=> path: the name of a file or directory
	=> name: name argument specifies the system variable to be queried. Ex: _PC_NAME_MAX, _PC_PATH_MAX, etc. Refer to man for more information
	=> RETURN: return current variable value if success, -1 but not set errno if does not have a limit in the system. -1 and set errno if error
```

get or set priority of process
``` bash
#include <sys/resource.h>

int getpriority(int which, id_t who);
	=> which: consts in the list below
	=> who: 0 for current process, process group, or user. Or process identifier for PRIO_PROCESS, process group identifier for PRIO_PGRP, user ID for PRIO_USER
	=> RETURN: the highest priority(lowest numberical value) of the specified processes. -1 and set errno if failed
int setpriority(int which, id_t who, int priority);
	=> which: consts in the list below
	=> who: 0 for current process, process group, or user. Or process identifier for PRIO_PROCESS, process group identifier for PRIO_PGRP, user ID for PRIO_USER
	=> priority: new priority wants to set. 0 is default priority. Lower number means higher priority. Range is -20 ~ 20. Only the super-users can lower the number. Normal user can only raise the priority
	=> RETURN: 0 if success, -1 if failed
```

which can be:

|name|description|
|-|-|
|PRIO_PROCESS|who is a process identifier|
|PRIO_PGRP|who is a group identifier|
|PRIO_USER|who is a user identifier|

get or set limitation of process
``` bash
#include <sys/resource.h>

int getrlimit(int resource, struct rlimit *r_limit);
	=> resource: consts in the list below
	=> r_limit: struct rlimit of the result
		struct rlimit {
			rlim_t rlim_cur;(soft limit, function will return error if soft limit is exceeded)
			rlim_t rlim_max;(hard limit, system will send signal to kill process exceeds the hard limit)
		}

		=> r_lim_t is an integer type
		=> a process can modify its soft limit(but need to lower than hard limit) and decrease hard limit. Only super-users can raise the hard limit
	=> RETURN: 0 if success, -1 and set errno if failed
int setrlimit(int resource, const struct rlimit *r_limit);
	=> resource: consts in the list below
	=> r_limit: struct rlimit to set limitation
	=> RETURN: 0 if success, -1 and set errno if failed
```

resource can be:
|name|description|
|-|-|
|RLIMIT_CORE|size limit of core dump in bytes|
|RLIMIT_CPU|time limit of  CPU in seconds|
|RLIMIT_DATA|size limit of data segment for a process in bytes|
|RLIMIT_FSIZE|size limit of file in bytes|
|RLIMIT_NOFILE|number limit of file can be opened|
|RLIMIT_STACK|size limit of stack in bytes|
|RLIMIT_AS|size limit of virtual memory allocated to a process in bytes|

get CPU usage time of process
``` bash
#include <sys/resource.h>

int getrusage(int who, struct rusage *r_usage);
	=> who: consts in the list below
	=> r_usage: result of structure of CPU time avaialble to current process after function call success
		struct rusage {
			struct timeval ru_utime;(used user time)
			struct timeval ru_stime;(used system time)
		};
		// #include <sys/time.h>
		struct timeval {
			time_t tv_sec;(seconds)
			suseconds_t tv_usec;(microseconds)
		};
	=> RETURN: 0 if success, -1 if failed

```

who `in getrusage()` can be:

|name|description|
|-|-|
|RUSAGE_SELF|only get current process's usage information|
|RUSAGE_CHILDREN|get current process's and child processes' usage information|
