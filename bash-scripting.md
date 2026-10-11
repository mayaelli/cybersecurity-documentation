## Bash Scripting

###What is Bash?
Bash is a scripting language that runs within the terminal on most Linux distros, as well as MacOS. Shell scripts are a sequence of bash commands within a file, 
combined together to achieve more complex tasks than simple one-liner and are especially useful when it comes to automating sysadmin tasks such as backups.

Additional resources to walk-through later: https://devhints.io/bash 

A bash script always starts with the following line of code at the top of the script.
```
#!/bin/bash
echo "Hello World"
whoami
ls
```

### Variables
Variables make it much easier to store data and rather than typing out the same thing in multiple places we could simply insert our variable with $var and then 
declare that to a certain value making it easier to fall back on if you do something wrong and need to change it. So how can we debug our code?
```
name = "Jammy"
echo $name
```

### Debugging lets you see what your script is doing line by line.
Bash Debugging
- Used to see what a bash script is doing line-by-line
- Helps find errors and bugs 
- Command: bash -x script.sh
  - Meaning: run script in debug mode (displays each command before it executes)
  - set -x (start debugging)
  - set +x (stop debugging)
    - set simply means 'settings switch'

### Parameters
$ = get value

$1 = first argument
$2 = second argument
$3 = third argument

./script.sh Alex Tony
$1 = Alex
$2 = Tony

read = ask user now
→ pauses script
→ waits for input

Parameters = input BEFORE execution
read = input DURING execution

💡 Brain Trigger:
     $0 = script name
     $1 = 1st argument
     $2 = 2nd argument
     $3 = 3rd argument
     $4 = 4th argument
     $# = number of arguments

Arguments start counting at $1, not $0, because $0 is reserved for the script itself.

### Array
Arrays are used to store multiple pieces of data in one variable, which can then be extracted by using an index. Most commonly notated as var[index_position].
Arrays use indexing meaning that each item in an array stands for a number.

array=(item1 item2 item3)

${array[index]}
→ get one item

${array[@]}
→ get all items

array[index]='new'
→ update

unset array[index]
→ delete


cars=('honda' 'audi' 'bmw')
${cars[1]}
audi

array[index]='new_value'
→ override/update element

### Conditional

if [ condition ]
then
    code
fi

if = check condition
then = run code if true
fi = end if

#### Comparison Operators:
-eq = ==
-ne = !=

-gt = >
-lt = <

-ge = >=

Example: if [ $count -eq 10 ]
Meaning: If count equals 10

#### File Checks
-f = file exists
-w = file writable

Example: if [ -f file.txt ]
Meaning: If files exists

💡 Brain Trigger
-f → file exists
-w → writable
-r → read
-d → directory
-x → executable

-eq → equal
-ne → not equal
-gt → greater than
-lt → less than
-ge → greater/equal

if → check
then → do
fi → end




