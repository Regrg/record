---
layout: post
---

## parse command option

``` bash
#include <unistd.h>

int getopt(int argc, char *argv[], const char *optstring);
	=> argc: argc passed from command
	=> argv: argv array passed from command
	=> optstring: option character list. If a character follow by ':' and a character, the character will be another argument to the character before ':'.(ex: f:l)
	=> RETURN: the next known option character in optstring. Return '?' if option character is unknown or there is no correct character after option with ':'. -1 if argument list ends.
extern char *optarg;
	=> a pointer points to the current argument character
extern int optind, opterr, optopt;
	=> optind: next argument in argv for next getopt() call
	=> opterr: default value is 1, will print error message to stderr when error. set to 0 if don't want to see error message.
	=> optopt: the last known option. Or if getopt() failed, the character causes failed will be stored here
```

``` bash
#include <getopt.h>
#define _GNU_SOURCE

int getopt_long(int argc, char *argv[], char *optstring, struct option *longopts, int *longindex);
	=> argc: argc passed from command
	=> argv: argv array passed from command
	=> optstring: option character list
	=> longopts: long option structure list
		strcut option {
			const char *name; (option name)
			int has_arg; (0 is no argument, 1 is must be one argument, 2 is either 0 or 1 argument)
			int *flag; (a flag number to replace val to returnm or NULL to return val itself)
			int val; (value to return when name is matched and if flag is NULL)
		};
		=> {0,0,0,0} should be appended at the end of structure array to reprepresent array ends
	=> longindex: the last known long option
	=> RETURN: if flag in strcut option is NULL, return val in the struct option. If flag is not NULL, function will store val in flag pointer and return 0. Return ? if option is unrecognized. Return : if option argument is missing. Return -1 if option list ends
```

## Environment variable
``` bash
#include <stdlib.h>

char *getenv(const char *name);
	=> name: desired variable key
	=> RETURN: corresponding value to the name, null if variable does not exist

	=> the return value is read-only string. copy it if want to modify it
int putenv(const char *string);
	=> string: should be format of "name=value"
	=> 0 if success, -1 if failed and errno will be set
```

an array list shows all environment variable
``` bash
#include <stdlib.h>

extern char **environ;
```

## time and date

get current time
``` bash
#include <time.h>

time_t time(time_t *tloc);
	=> tloc: returned time will put here if not NULL
	=> RETURN: return second since Jan, 1, 1970(Linux-like systems are the same. MS-DOS systems are Jan, 1, 1980)
	
	=> time_t is a type of positive long integer, which is defined in time.h
```

estimate time duration
``` bash
#include <time.h>
double difftime(time_t timwe1, time_t time2);
	=> time1: the start time
	=> time2: the end time
	=> RETURN: double type of calculation "end time - start time". Express in second
```

transform time to special structure and become human-friendly
``` bash
#include <time.h>

struct tm *gmtime(const time_t timeval);
	=> timeval: time to transform to structure
	=> RETURN: struct tm of the timeval
```

struct tm include these variables at least:

|variable|description|
|-|-|
|int tm_sec|second(0-60)|
|int tm_min|minute(0-59)|
|int tm_hour|hour(0-23)|
|int tm_mday|day of month(1-31)|
|int tm_mon|month of year(0-11)|
|int tm_year|year since 1900|
|int tm_wday|day of week(Sunday is 0)|
|int tm_yday|dat of year(0-365)|
|int tm_isdst|is Daylight saving time|

transform time to special structure and become human-friendly as gettime() does but add GMT and DST effect
``` bash
#include <time.h>

struct tm *localtime(const time_t *timeval);
```

transform strcut tm back to time_t
``` bash
#include <time.h>

time_t mktime(struct tm *timeptr);
	=> timeptr: strcut tm wants to transform
	=> RETURN: time_t value or -1 if failed
```

transform to MORE human-friendly date and time format
``` bash
#include <time.h>

char *asctime(const strcut tm *timeptr);
	=> timeptr: struct tm wants to transform
	=> RETURN: human-readable string of date and time
char *ctime(const time_t *timeval);
	=> timval: time_t wants to transform
	=> RETURN: human-readable string of date and time

	=> return format is fixed: ex "Sun Jun 9 12:34:56 2007\n\0"
```

print date and time like sprintf() does
``` bash
#include <time.h>

size_t strftime(char *s, size_t maxsize, const char *format, struct tm *timeptr);
	=> s: buf to put output string
	=> maxsize: lenth of output string
	=> format: format symbol to output date and time
	=> timeptr: struct tm wants to transform
```

scan date and time like sscanf() does
``` bash
#include <time.h>

char *strptime(const char *buf, const char *format, struct tm *timeptr);
	=> buf: buf to put scanned string
	=> format: sscanned type format
	=> timeptr: struct tm to put output date and time information
	=> RETURN: pointer in buf indicating conversion error or NULL if failed.
	Use this pointer to decide whether the conversion is ended or not
```

|conversion operator|description|
|-|-|
|%a|abbreviated weekday name|
|%A|weekday name|
|%b|abbreviated month name|
|%B|month name|
|%c|time and date|
|%d|day of month(0-31)|
|%H|hour(0-23)|
|%I|hour(01-12)|
|%j|day of year(001-366)|
|%m|month of year(01-12)|
|%M|minute(01-59)|
|%p|am or pm|
|%S|second(00-60)|
|%u|weekday(1=Monday)|
|%U|week number of the year, started by Sunday(01-53)|
|%V|week number of the year, started by Monday(01-53)|
|%w|day of week(0=Sunday)|
|%x|national representation of the date|
|%X|national representation of the time|
|%y|year without century(00-99)|
|%Y|year with century|
|%Z|time zone name|
|%%|symbol %|

