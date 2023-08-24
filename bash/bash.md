# Bash
### wrap variable with quotes(`'` or `"` are both okay)
example:
``` bash
#!/bin/sh

echo "Please enter yes or no"
read answer

if [ "$answer" = "yes" ] then # here
	echo "answered yes"
elif [ "$answer" = "no" ] then # and here
	echo "answered no"
else
	echo "unrecognized answer"
	exit 1
fi
exit 0
```
If do not wrap, runtime error occurs when the content is empty

### until keyword is similar to while keyword. But break until the condition is true

example:
```bash
#!/bin/sh
read command
until [ "$command" == "stop" ]; do
	sleep 60
	read command
done

exit 0
```

### shift keyword shifts parameter list. $0 does not change. $2 becomes $1, $3 becomes $2 and so on

example:
```bash
foo()
{
	while [ "$1" != "stop" ]; do
		echo "parameter $1"
		shift # here
	done
}

foo "p1" "p2" "p3" "p4 "stop"
```