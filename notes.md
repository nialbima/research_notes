# SHELL SCRIPTING

## UDEMY COURSE NOTES

These are notes on shell scripting syntax in OSX Terminal, collected in one place for easy reference. They're based on the listed uDemy course, but will be expanded over time.


Course is [here](https://www.udemy.com/shell-scripting-linux/learn/#/, "Course URL").

### Section 1 - Admin Stuff:
- there is basically nothing useful here, admin course stuff.

### Section 2 - Basics:

##### Syntax Notes:
- ```man``` WHATEVER tells you whatever
- so does ```info```
- variables can't start with numbers, but can include them
- valid characters are [ABCabc_0-9]
- valid syntax:
- ```VARIABLE_NAME="value"```
- ```VARIABLE_NAME=$(command)```
  - this is set equal to the result of that command

##### Interpolating Into Strings:
- ```$ { VARIABLE }``` if you need to append to the text
- ```$VARIABLE``` if you don't.

You need to tell the script which interpreter to use:
- for instance:
  - ```#!/bin/ruby``` for ruby
  - ```#!/bin/bash``` for bash

##### Conditionals:
  - [ condition to test for ]
  - status: 0 is true, 1 is false.
  - flags for files:
    - ```[ -e FILE ]``` : exists
    - ```[ -d FILE ]``` : file is a directory
    - ```[ -f FILE ]``` : file exists and is a regular file
    - ```[ -r FILE ]``` : file is readable
    - ```[ -s FILE ]``` : file exists and is not empty
    - ```[ -w FILE ]``` : file is writable
    - ```[ -x FILE ]``` : file is executable
  - strings:
    - ```[ -z STRING ]``` : string is empty
    - ```[ -n STRING ]``` : string is not empty
    - ```[ STRING1 = STRING2 ]``` : equal
    - ```[ STRING1 != STRING2 ]``` : not equal
  - num:
    - ```[ arg1 -eq arg2 ]``` : true if equal
    - ```[ arg1 -ne arg2 ]``` : true if not equal
    - ```[ arg1 -lt arg2 ]``` : true if arg1 < arg2
    - ```[ arg1 -le arg2 ]``` : true if arg1 <= arg2
    - ```[ arg1 -gt arg2 ]``` : true if arg1 > arg2
    - ```[ arg1 -ge arg2 ]``` : true if arg1 >= arg2


##### If/then Statement Syntax:

    if [ condition-is-true ]
    then
      command 1
      command 2
      ...
      command n
    <!-- elif is elsif -->
    elif [ other-condition-is-true]
    then
      command x
    else
      command y
    fi

##### For Loop syntax:

    for VARIABLE_NAME in ITEM_1 ITEM_N
    do
      command 1
      command 2
      command n
    done

###### Example:

    #! /bin/bash
    COLORS="red green blue"
    for COLOR in $COLORS
    do
      echo "COLOR: $COLOR"
    end

##### Positional Parameters:
- i.e. where on the command line a script falls
- for ```$ script.sh p1 p2 p3```
  - $0 is script.sh
  - $1 is p1
  - $2 is p2
  - $3 is p3
  - $@ is all of three
- use ```read``` to accept input from the user (like prompt)

##### Comments:
- ruby syntax, \#
- \# at start of line : ignore entire line
- \# in middle of line : everything to the right of the #

so mixing these together:
- we have variables
- we can use loops
- we can set conditions
- we can iterate through objects

### Section 3 - Exit Statuses and Return Codes:
- checking ```exit``` status of a command, do conditions based on them
- exit statuses go from 0 to 255
- 0 is great
- everything else is an error
- man or info tell you what's up there

##### More Variables:
- $? contains the status of the previously executed command

###### Example:

    #! /bin/bash
    HOST="google.com
    ping -c 1 $HOST
    if [ "$?" -eq "0" ]
    then
      echo "$HOST reachable."
    else
      echo "$HOST unreachable."
    fi

- This would execute a ```ping``` command and make sure it returns the right response code.

#### And/Or/Semicolons:
- ```&&``` = AND : executes both commands, or evaluates both conditionals.
  - will only fire if both succeed. it's a gate.
- ```||``` = OR : executes the first command always.
  - if it fails, the second command executes. it's a net.
- ```;``` = KEEP GOING : separates commands.
  - the chain of commands will fire/doesn't care at all about success v. failure

#### Exit Commands:
- you can specify an exit status for a script using ```exit```.
- in Terminal, you can check exit status with ```echo "$?"```
- you can use ```man <command-name>``` to check what an exit code means
  - there's usually a list.

### Section 4 - Functions:
- yes yes yes, Functions are DRY-er. shhhh, udemy.
  - who is taking this that doesn't want to use functions?
- reusable code. defined before use. accepts parameters.

#### Syntax
shell scripts use JS-lke syntax for functions:

    function function-name() {
     # code goes here.
   }

but also doesn't care:

    function-name() {
     #code goes here.
    }

Calling them isn't too complicated:

    #!/bin/bash
    function hello() {
      echo "HEYA"
    }
    hello

So basically: JS-like/C-style definitions. Ruby-like invocations. Brackets.

Functions have to be declared before they are used. if you invoke a different in another function, you need to make sure you're calling it *after* it's been defined.
  - so just put them all at the top level.

#### Params:
We can use those $1, $N variables in functions. but we CANNOT use $0 to refere to the function name, because that's reserved for the script itself.
  - so no recursive anything.

##### Example:
    #!/bin/bash
    function hello() {
      for NAME in $@
      do
        echo "Hello $NAME"
      done
    }
    hello Pluto Magnum Zoolander

#### Scope:
- variables are *by default global*.
- variables get defined when they're called. it's A) therefore not available until it's called by another function.
- you can define a local var with ```local LOCAL_VAR=1```
  - use local vars in functions.

#### Exit Status (Return Code)
- functions have an exit status
- they return a code:
  - ```return (RETURN_CODE)```
- same as on the CMD

### Wildcards:
- wildcards are characters or strings used for pattern matching.
  - also called globs or patterns or greps
  - WHAT IS UP, REGEXES
  - wildcards can be used with most commands
    - ls
    - rm
    - cp
    - etcetera
- these are all valid on both the terminal and within a shell script.

#### Types:
  - ```*``` matches 0 or more characters, and returns all files that match that string (i.e. \*.txt returns all text files in a dir)
    - \*.text
    - a\*
    - a\*.text
    - can be combined with other wildcards to control the number of objects created
  - ```?``` matches exactly one character and returns all files that match that condition
    - ?.text
    - a?
    - a?.text
    - can be combined with other wildcards to control the number of objects created
  - ```[]``` - a character class
    - matches any of the chars included between the brackets
    - [aeiou]
    - matches exactly 1 by default, but you can combine with * for "all matches"
    - just like regex
  - ```[a-g]``` creates a character range, as does ```[3-6]```

#### Named Character Class:
  - there are also named clases, like ```\w```,```\S```, etcetera
  - you can also use the following named character classes:
    - ```[[:alpha:]]``` matches the alphabet
    - ```[[:alnum:]]``` matches alphanumeric characters (upper or lower letters, decimals )
    - ```[[:digit:]]``` matches digits
    - ```[[:lower:]]``` matches lowercase letters
    - ```[[:space:]]``` matches whitespace
    - ```[[:upper:]]``` matches uppercase letters
  - you can also match a wildcard character by escaping with a ```\```
    - i.e. ```\*``` if you have a file with an asterisk (also don't do this, and judge those who do a LOT)

#### Why?
  - regexes are great is why. super useful.

#### How do I use this in scripts?
  - A) first, make sure you're in the right directory
    - e.g. if this cares about files in a specific dir, include ```cd that/dir```
    - seems like passing in a path is a good idea? lets you use $1 to go grab stuff from a specific folder.
  - B) next, make sure you're targeting the right files
  - C) use the regex!

### Case Statements:
- case statements are useful. you execute a number of commands on a ```$VARIABLE```, like so:

    case "$VARIABLE" in
      pattern_1)
        # commands go here
        ;;
      pattern_n)
        # commands go here.
        ;;
    esac

- basically, you pass in a wildcard-enabled string for pattern_1, etcetera, and execute functions based on a match (i.e. it's a case statement)

##### Example:

    case "$1" in
      start)
        /usr/sb/sshd
        ;;
      stop)
        kill $(cat /var/run/sshd.pid)
        ;;
    esac (also: who let somebody make this syntax official, this is dumb)

- ```|``` is an OR. you can use it to specify multiple matches attached to the same set of commands.

i.e. this:

    case "$1" in
      y | yes
        echo "answered yes"
        ;;
      n | no
        echo "answered no"
        ;;
    esac

- This'll let you respond with either y or yes and get a positive answer

- so basically: pass in a var, and compare it against some wildcards, executing scripts based on what gets matched.
  - still just a cleaner replacement for if

### Logging :
- logs are files, detailing wtf happened during execution of a script
- seems like this is linux-specific
- with ```syslog```, you can see all the objects in the computer's log
  - works on OSX.
  - they come from various locations (this list might be linux-specific, donno):
    - ```kern```
    - ```user```
    - ```mail```
    - ```daemon```
    - ```auth```
    - ```local0```
    - ```local7```
  - levels of severity from last to most:
    - ```debug```
    - ```info```
    - ```notice```
    - ```warning```
    - ```err```
    - ```crit```
    - ```alert```
    - ```emerg```
  - they can go in multiple locations

- basically, you use this by supplying a message to the ```logger``` command
  - defaults to ```notice``` severity and ```user``` facility
  - there are a lot of options here. ```man logger``` seems useful.
- no more of that. just use ```logger "message"``` with flags to make your programs speak to you.

### While Loops:
- standard syntax is pretty familiar

##### Example Syntax:
    while [ condition_is_true ]
    do
      #commands change the condition
      command 1
      command 2
      ...
      command n
      sleep.1
    done

- it's a while loop. if any command inside does not return an ```exit 1``` (i.e. fails) the condition will stop.
  - at which point you use cmd-c
- you can use ```while true``` as the condition, you can set up infinite, perpetual loops until you kill them.
  - ```sleep.number_of_seconds``` puts the loop to sleep for a given number of seconds.
- you can use a counter and ```[ $COUNTER -lt A_NUMBER ]``` to loop a specific number of times with ```((COUNTER_NAME++))```

##### Get User Name:

    while [ "$CORRECT" != "y" ]
    do
      read -p "Enter your name: " NAME
      read 0- "ls $(NAME) correct? " CORRECT
    done

#### Okay. Why:
- first off, if you need to make an infinite background script, this'll do it.
- if you need to execute a series of commands a specific number of times, but don't know exactly how many, it'll do it.
- but also: this is how shell scripts use file input. a while loop triggering on ``` while read LINE``` will examine the file passed in as an argument until it runs out of lines

##### Print out each line:

    LINE_NUM=1
    while read LINE
    do
      echo "$(LINE_NUM) : $(LINE)"
      ((LINE_NUM++))
    done < /log/files

- for instance, you can use the ```|``` to pipe the results of a GREP wildcard search into a ```while read LINE``` loop.
- and you can pass in variables to get more info out of the input.
- etcetera.
- you can also pass in commands from other syntaxes than BASH (MySQL, postgres, ruby, python, etcetera). it's pretty cool.
- ```break``` exits the loop
- ```continue``` restarts the loop at the next iteration.

### Debugging
- when you call a script, you can pass in some useful flags to test them
- you can either use them at the head of scripts, or on the command line
  - in a script: ```#!/bin/bash -x```
  - cmd (setting it as a mode): ```set -v```
    - will return all verbose objects

#### Useful Flags:
- ```-x``` prints commands as they execute
  - calling ```set -x``` in the terminal is pretty fun
    - prints out every command executed whenever you hit return.
    - ```set +x``` reverses this (do that).
- ```-e``` exit on error
  - terminates script when it blows up.
  - can be combined with -x
- ```-v``` prints input lines as they're read.
  - also combinable


# Bash!
