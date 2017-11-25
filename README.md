# bash_cheat_sheet
Compilation of bash scripting useful tips &amp; tricks

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
