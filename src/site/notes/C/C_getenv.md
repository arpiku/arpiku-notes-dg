---
{"dg-publish":true,"permalink":"/c/c-getenv/"}
---

# Environment Variable
An [_environment variable_](https://en.wikipedia.org/wiki/Environment_variable) is a variable whose value is set outside the program, typically through functionality built into the operating system or microservice. An environment variable is made up of a name/value pair, and any number may be created and available for reference at a point in time.

We can use use something like [[Python/python_os\|python_os]] to get the enviornment variables from the os, in C we can do that by using the following code:
~~~C
#include <stdio.h>
#include <stdlib.h>

int main () {
   printf("PATH : %s\n", getenv("PATH"));
   printf("HOME : %s\n", getenv("HOME"));
   printf("ROOT : %s\n", getenv("ROOT"));

   return(0);
}

//The decleration looks like this 
char *getenv(const char *name)

~~~

