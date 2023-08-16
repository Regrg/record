---
layout:post
---

#### parameter expansion

add curly brackets to replace parameter i
``` bash
#!/bin/sh
for i in 1 2
do
	my_secret_process ${i}_tmp
done

exit 0
```

``` bash
#!/bin/sh
unset foo

# foo is null, print -bar behind colon
echo ${foo:-bar}

foo=fud
# foo is NOT null, print fud
echo ${foo:-bar}

foo=/usr/bin/X11/startx
# asterisk means 0 or more. From the left, this will remove anything to the leftest backslash and print usr/bin/X11/startx
echo ${foo#*/}
# From the left, this will remove anything to the rightest backslash and print startx
echo ${foo##*/}

bar=/usr/local/etc/local/networks
# From the right, this will remove anything to the rightest string "local" and print /usr/local/etc/
echo ${bar%local*}
# From the right, this will remove anything to the leftest string "local" and print /usr/
echo ${bar%%local*}

exit 0
```
