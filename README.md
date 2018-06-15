# Bash cheat sheet
Compilation of bash scripting useful tips &amp; tricks

* [Check script syntax](#check-script-syntax)
* [Arrays in bash](#arrays-in-bash)
  * [Declare arrays](#declare-arrays)
  * [Access array elements](#access-array-elements)
  * [Traverse an array](#traverse-an-array)
  * [Extract elements of an array](#extract-elements-of-an-array)
  * [Search and replace in array elements](#search-and-replace-in-array-elements)
  * [Remove an element](#remove-an-element)
  * [Copy an array](#copy-an-array)
  * [Concatenate two arrays](#concatenate-two-arrays)
  * [Delete an array](#delete-an-array)
* [Generate random string](#generate-random-string)
* [Split string and create array](#split-string-and-create-array)
* [Create semaphore files](#create-semaphore-files)
* [Add a clean-up function to a script](#add-a-clean-up-function-to-a-script)
* [Find a substring](#find-a-substring)
* [Variable indirect references](#variable-indirect-references)

## Check script syntax
Syntax can be checked using `bash -n`

```
$ cat test.sh
#!/bin/bash

declare -a my_array=('a' 'b' 'c')

for elem in ${my_array[@]};do
  echo ${elem}

echo "End"

$ bash -n test.sh
test.sh: line 9: syntax error: unexpected end of file
```

## Arrays in bash

### Declare arrays
There are two ways to declare an array:

* Assign values:
```
unix[0]='Debian'
unix[1]='Red hat'
unix[2]='Ubuntu'
```

* Initialize the array in declaration:
```
declare -a unix=('Debian' 'Red Hat' 'Ubuntu')
```

An empty array can be declared using empty parenthesis:
```
empty_array=()
```

### Access array elements

Using index: 
```
echo ${unix[1]}
```
All the elements:
```
echo ${unix[@]}
```
Length of array:
```
echo ${#unix[@]}
```
Length of an element:
```
echo ${#unix[3]}
```

### Traverse an array
```
for elem in ${unix[@]}; do
  echo $elem
done
```

### Extract elements of an array

* Extract by offset and length for an array
```
$cat arraymanip.sh
Unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora' 'UTS' 'OpenLinux');
echo ${Unix[@]:3:2}

$./arraymanip.sh
Suse Fedora
```

* Extract by offset and length for a given element
```
$cat arraymanip.sh
#! /bin/bash

Unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora' 'UTS' 'OpenLinux');
echo ${Unix[2]:0:4}

./arraymanip.sh
Ubun
```

### Search and replace in array elements
```
$cat arraymanip.sh
#!/bin/bash
Unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora' 'UTS' 'OpenLinux');

echo ${Unix[@]/Ubuntu/SCO Unix}

$./arraymanip.sh
Debian Red hat SCO Unix Suse Fedora UTS OpenLinux
```

### Remove an element
An element can be removed setting it to null
```
$cat arraymanip.sh
#!/bin/bash
unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora' 'UTS' 'OpenLinux');

unset unix[1]      # Same than set to null
echo ${unix[1]}
```

Other option is creating a copy of the array, removing the element that matches a pattern, so the array will not contain null values:
```
$ cat copy_filtered_by_pattern.sh
#!/bin/bash
declare -a unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora');
declare -a patter=( ${Unix[@]/Red*/} )    # Removes all elements starting with "Red"
echo ${patter[@]}         

$ ./copy_filtered_by_pattern.sh
Debian Ubuntu Suse Fedora
```


### Copy an array
```
unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora' 'UTS' 'OpenLinux');
Linux=("${unix[@]}")
```

### Concatenate two arrays
```
$cat arraymanip.sh
#!/bin/bash
Unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora' 'UTS' 'OpenLinux');
Shell=('bash' 'csh' 'jsh' 'rsh' 'ksh' 'rc' 'tcsh');

UnixShell=("${Unix[@]}" "${Shell[@]}")
echo ${UnixShell[@]}
echo ${#UnixShell[@]}

$ ./arraymanip.sh
Debian Red hat Ubuntu Suse Fedora UTS OpenLinux bash csh jsh rsh ksh rc tcsh
14
```

### Delete an array
```
$cat arraymanip.sh
#!/bin/bash
Unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora' 'UTS' 'OpenLinux');
Shell=('bash' 'csh' 'jsh' 'rsh' 'ksh' 'rc' 'tcsh');

UnixShell=("${Unix[@]}" "${Shell[@]}")
unset UnixShell
echo ${#UnixShell[@]}

$ ./arraymanip.sh
0
```

### Split string and create array
```
string="a,b,c,d"
declare -a array
read -r -a array <<< `echo $string | sed -r 's/,/ /g'`
```

# Generate random string
Generate a random string of 32 characters, containing only letters and numbers.
```
$ cat /dev/random | tr -dc 'a-zA-Z0-9' | fold -w 32 | head -n 1
DXGuCD6XAUXMUjRgu8StgTOq0Ayij4uf
```

# Create semaphore files
`lockfile` command can be used to create semaphore files. Option `-r` indicates the number of retries before returning error.
```
[Terminal #1] $ lockfile -r 0 /tmp/the.lock
[Terminal #1] $

[Terminal #2] $ lockfile -r 0 /tmp/the.lock
[Terminal #2] lockfile: Sorry, giving up on "/tmp/the.lock"
```
The created files are read-only, so they have to be deleted with `rm -f`
```
[Terminal #1] $ rm -f /tmp/the.lock
[Terminal #1] $

[Terminal #2] $ lockfile -r 0 /tmp/the.lock
[Terminal #2] $
```

# Add a clean-up function to a script
This code will execute the `clean_up` function when the script ends in any case.
```
trap clean_up EXIT

function clean_up
{
  echo "the end"
}
```
The exit code of the clean-up function will not affect the exit code of the script:
```
$ cat cleanup.sh
#!/bin/bash

trap clean_up EXIT

function clean_up
{
  echo "the end"
  return 0
}
exit 1

$./cleanup.sh
the end

$echo $?
1
```

# Find a substring
```
$ cat substring.sh
#!/bin/bash

string="Once upon a time"
substring="upon"
if [[ "${string}" =~ "${substring}" ]]; then
  echo "found!"
fi

$ ./substring.sh
found!
```

# Variable indirect references
```
$ cat indirect_reference.sh 
#!/bin/bash

variable_to_use="name"
name="John"
echo ${!variable_to_use}

$ ./indirect_reference.sh
John
```
