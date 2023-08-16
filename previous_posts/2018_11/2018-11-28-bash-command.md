---
layout: post
---

- when use `variable` in condition, remember to wrap it with ""(double quotes) to prevent run error if user press ENTER into the variable
- by bash definition, `remember to put space to separate each words in condition`. Due to space is defined as separator in bash, be careful, `put or not put space can cause different effect`.

example
``` bash
#!/bin/sh

echo "Please enter yes or no"
read answer

// if answer variable is not wrapped by double quote, error will occur when user directly press ENTER. Or remove the space wrapping the condition will cause error
if [ "$answer" = "yes" ] then
	echo "answer yes"
elif [ "answer" = "no" ] then
	echo "answer no"
else
	echo "unrecognized answer"
	exit 1
fi
exit 0
```

### for loop treats every variable as `string`. Can put integer, character, float number, etc behind for loop

sample
``` bash
#!/bin/sh

for foo in bar fud 43 22.02
do
	echo $foo
done
exit 0
```

can use `$(command)` to get command result. But remember: errors or result will only be dispplayed when this program runs.
``` bash
#!/bin/sh

for foo in bar $(f*.sh); do
	echo $file
done
exit 0
```
### while to loop through loop with no specific end times

sample
``` bash
#!/bin/sh

read trythis
while [ "$trythis" != "secret" ]; do
	echo "Sorry, try again"
	read trythis
done
exit 0
```

### until is similar to while. But loop until condition is true

sample
``` bash
#!/bin/sh
// who command get the result of logged in users
until who | grep "$1" > /dev/null
do
	// compare to parameter every 60 seconds
	sleep 60
done

echo "$1 has logged in"
exit 0
```

### case: like switch statement in C/C++.  But here can compare `different variables` with different patterns. 
Wildcard can be used here. But, be careful, `only one pattern will be matched and execute the statement although multiple patterns comply the variable rules.`

example
``` bash
echo "answer yes or no"
read answer"

case "$answer" in
	// can directly tuple possible patterns together
	yes | y | Yes | YES)	echo "answer yes";;
	n* | N*) echo "answer no";;
	*) echo "unrecognized answer";;
esac

exit 0
```

### function: just like function in C/C++. But contain some different characteristics:
- function will automatically return the result of the last command. But should use `return` to return primitive value. Or can store value inside a variable and return.

``` bash
#!/bin/sh

yes_or_no() {
    echo "is your name $1?"
    while true
    do
        echo "yes or no"
        read x
# return 0 is true, not 0 is false
        case "$x" in
            y) return 0;;
            n) return 2;;
            *)  echo "answer yes or no"
        esac
    done
}

echo "parameter is $*"

# send parameter to function, put parameter behind it
# return value 0 is true, not 0 is false
if yes_or_no "$1" ; then
    echo "Hi $1, nice name"
else
    echo "never mind"
fi
exit 0
```



