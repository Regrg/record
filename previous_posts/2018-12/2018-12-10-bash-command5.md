---
layout: post
---

#### file descriptor access
``` bash
#include <stdio.h>
FILE *fopen(const char *filename, const char *mode);
	filename: filename want-to-open
	mode: "r"(read), "w" (write), "b"(binary) or "a"(append)
	RETURN: NULL if fail, FILE* pointer if success
```

``` bash
#include <stdio.h>
size_t fread(void *ptr, size_t size, size_t nitems, FILE *stream);
	ptr: buffer to store nitems * size data
	size: size of read data
	nitems: number of size
	stream: file descriptor want-to-read
	RETURN: number of size. Can be equal or smaller than nitmes if reach EOF

size_t fwrite(void *ptr, size_t size, size_t nitmes, FILE *stream);
	=> like fread, write buffer ptr data into file descriptor stream
	=> RETURN number of size write into file descriptor stream
```

call fclose after finish using file. Success or error can be access by program after fully close file descriptor
``` bash
#include <stdio.h>
int fclose(FILE *stream);
	stream: file descriptor want-to-close
	RETURN: 0 if success or EOF if failed
```

flush buffered data into file descriptor.
- fclose() will also call fflush before close the file descriptor
``` bash
#include <stdio.h>
int fflush(FILE *stream);
	stream: flush buffer data into stream instantly
	RETURN: 0 if success, EOF if failed
```

lseek() similar function.
- lseek returns byte of offset from file beginning. fseek return success or failed
``` bash
#include <stdio.h>
int fseek(FILE *stream, long int offset, int whence);
	stream: target file descriptor
	offset: offset from the whence position in the file descriptor
	whence: SEEK_SET(start of file), SEEK_CUR(current position), SEEK_END(end of file), etc
	RETURN: 0 if success, -1 if failed
```

read or write a char to file descriptor
``` bash
#include <stdio.h>

int fgetc(FILE *stream);
int getc(FILE *stream);
int getchar();

int fputc(int c, FILE* stream);
int putc(int c, FILE *stream);
int putchar(int c);

=> returning int instead of char is to indicate -1(EOF) when failed
```

``` bash
#include <stdio.h>
char *fgets(char *s, int n, FILE *stream);
	s: buffer to stored read data
	n: maximum number of character read or encounter \n or EOF
	stream: file descriptor want-to-read
	RETURN: pointer to s or NULL if encounter EOF or error
	=> will read only 0 ~ n-1 characters because it will add \0 at the end of string
char *gets(char *s);
	=> like fgets(). It will add \0 at the end of input but read from stdin
	=> easy to cause buffer overflow due to no input character limitation on gets()
```

## Printf, Scanf
`if scanf() cannot get corresponding format of input, it will produce infinite loop`

``` bash
#include <stdio.h>

int printf(const char *format);
int sprintf(char *s, const char *format, ...);
	=> print result and \0 at the end of string into s
int fprintf(FILE *stream, const char *format, ...);
	=> print result into file descriptor stream

	=> RETURN: the total number of characters written is returned

int scanf(const char *format, ...)
	=>> better to use fgets() or sscanf() to read string by length in order to prevent buffer overflow;
int fscanf(FILE *stream, const char *format, ...);
	=> scan result from stream
int sscanf(const char *s, const char *format, ...);
	=> scan result from s

	=> RETURN: number of successuful input items assigned. 0 if no items assigned. -1 if failed or EOF
```

printf control format

|format|parameter|result|
|-|-|-|
|%10s|"Hello"|     Hello|
|%-10s|"Hello"|Hello     |
|%10d|1234|      1234|
|%-10d|1234|1234      |
|%010d|1234|0000001234|
|%10.4f|12.34|        12.3400|
|%\*s|10, "Hello"|     Hello|

`if parameter is longer than format limit. By POSIX definition, result will auto-extend`

## stream error

- when function produce error, the errno will record the error code of the error.
- `should copy errno number first and read it because every function can alter errno value, even fprintf() will.`

``` bash
#include <errno.h>

extern int errno;
```

``` bash
#include <stdio.h>

int ferror(FILE *stream);
	=> RETURN: 0 if stream is success. NOT zero if stream has error
int feof(FILE *stream);
	=> 0 if stream encounters EOF. NOT zer if not EOF
void clearerr(FILE *stream);
	=> clear error like EOF or stream error
```

``` bash
#include <stdio.h>
int fileno(FILE *stream);
	=> stream: target file stream
	=> RETURN: file descriptor of stream, or -1 if failed

# provide file descriptor a file stream to operate
FILE *fdopen(int fildes, const char *mode);
	=> fildes: file descriptor want-to-open stream
	=> mode: "r", "w", "a" of file access mode
	=> RETURN: file stream points to file descriptor
```
