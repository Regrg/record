---
layout: post
---

#### use "." to execute a script command
- Normally, run `./ANOTHER_SCRIPT`command in script will fork a child shell and execute the command. The environment variable will be independent each other.
- But if run `. ./ANOTHER_SCRIPT` comamnd in script will run it in the same script. The environment variables will be shared and any changes toward environment variables will be visible to each other.

##### Example
There is one script with classic variables
``` bash
#!/bin/sh

version=classic
PATH=/usr/local/old_bin:/usr/bin:/bin:.
PS1="classic> "
```

There is one script with latest variables
``` bash
#!/bin/sh

version=latest
PATH=/usr/local/new_bin:/usr/bin:/bin:.
PS1="latest version> "
```

run script to print environment variable
``` bash
#!/bin/sh
version="original version"

# print "original version"
echo $version

# script will fork this to another script. Environment variable is still "original version"
./classic_set.sh
echo $version

# script will run this here. The environment variable will change accordingly to "latest"
. ./latest_set.sh
echo $version

exit 0
```


#### exit: end script. If a exit number is not provided, it will return the last command execution state. It is recommended to add exit number behind.
- number `0` represents `success`. `1~125` are `self-defined error` code. number `above 126` are `reserved for system`

#### export: export variable, and child script can also see the variable
- `set -a` or `set -allexport` will export `all` variables.

#### printf: like C/C++. Printf to print on screen
- use printf instead of using echo. Printf can be more specify to print on screen
- printf in script cannot print `float number` because shell only support Integer.

``` bash
#!/bin/sh

printf "%s\n" hello
printf "%s %d %s\n" "abc cde" 12 heyy

exit 0
```

#### set: set parameter variable. The variable must be separated by space
- set script parameter($1, $2, $\*) with variable behind
``` bash
#!/bin/sh

echo the date is $(date) # Thu Nov 29 15:59:40 CST 2018
set $(date)
echo the month is $2

exit 0
```

#### shift: shift parameter list. $0 doesn't change. Remove $1, $2 becomes $1, $3 becomes $2, etc.
- adding number N behind shift will directly shift N times

``` bash
#!/bin/sh

while [ ["$1" != "" ]; do
	echo "$1"
	shift # shift parameter to $1
done

exit 0
```

#### trap: trap signal with certain command
- put commands want-to-protect after trap. Before trap, script won't receive desired signal
- ex: `trap 'exit 1' INT` (trap command signal). It will exit script with 1 exit code when see signal interrupt
- use `trap - SIGNAL` to reset signal default behavior
- use `trap "" SIGNAL` to ignore signal

``` bash
#!/bin/sh

trap 'exit 1' INA	# set signal INTERRUPT with comand "exit 1"T
while :; do
	echo loop...
	sleep 1
done

# above is infinite loop. This exit will never be executed
exit 0
```

#### unset: unset a variable
- set variable to null is similar. But unset command is to remove this variable in the environment but variable still exists if set to null
