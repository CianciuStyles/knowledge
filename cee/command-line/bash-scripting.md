# Bash scripting

### Redirect to file

```bash
# Write hello to file.txt (replacing the whole contents of the file)
echo "hello" > file.txt

# Append hello to the end of file.txt
echo "hello" >> file.txt
```

### Comments

```bash
# this is a single line comment

: '
this
is a
multiline comment'
```

### Conditional statements

```bash
# define a variable
count=10

# leave spaces before and after brackets
# reference variables with $
# eq, ne, lt, gt, le, ge
if [ $count -eq 10 ] 
then
    echo "first condition is true"
elif (( $count <= 9 ))
then
    echo "second condition is true"
else
    echo "all conditions are false"
fi # close if statement

age=10

# and statement - alternatives are
# if [[ "$age" -gt 18 && "$age" -lt 40 ]]
# if [ "$age" -gt 18 -a "$age" -lt 40 ]
if [ "$age" -gt 18 ] && [ "$age" -lt 40 ]
then
    echo "Age is correct"
else
    echo "Age is not correct"
fi

# or statement - alternatives are
# if [ "$age" -gt 18] || [ "$age" -lt 40 ]
# if [[ "$age" -gt 18 || "$age" -lt 40 ]]
if [ "$age" -gt 18 -o "$age" -lt 40 ]
then
    echo "Age is correct"
else
    echo "Age is not correct"
fi

# switch statement
car=$1 # read first argument
case $car in
    "BMW" )
        echo "It's a BMW" ;;
    "MERCEDES" )
        echo "It's a Mercedes" ;;
    "TOYOTA" )
        echo "It's a Toyota" ;;
    * ) # default case
        echo "Unknown car" ;;
esac # close switch statement
```

### Loops

```bash
number=1

# while repeats when condition is true
# until repeats when condition is false
while [ $number -lt 10 ]
do
    echo "$number"
    # $(( ... )) allows for arithmetic
    number=$(( number+1 ))
done

# for larger ranges use {0..20}
# for different increments use {0..20..2}
for i in 1 2 3 4 5
do
    if [ $i -eq 5 ]
    then
        break
    fi
    echo $i
done

for (( i=0; i<5; i++ ))
do
    if [ $i -eq 3 ] || [ $i -eq 5 ]
    then
        continue
    fi
    echo $i
done
```

### Script input

```bash
# print first, second, and third argument to the scrpt
# $0 is the script name
echo $1 $2 $3

# $@ = all the inputs
# (...) creates an arrary
args=("$@")
echo ${args[0]} ${args[1]} ${args[2]}
# print number of arguments
echo $#

# read line reads from input and saves into variable line
while read line
do
    echo "$line"
done < "${1:-/dev/stdin}" # read from first argument, /dev/stdin as default
```

### Script output

```bash
# redirects stdout (1) to file.txt, and stderr (2) to file2.txt
ls -al 1>file.txt 2>file2.txt

# redirects 2 to whatever 1 is pointing to
ls -al > file.txt 2>&1

# redirects both stdout and stderr
ls -al >& file.txt
```

### Compare strings

```bash
echo "enter first string"
read str1

echo "enter second string"
read str2

# string equality
# \< and \> are for string comparison 
if [ "$str1" == "$str2" ]
then
    echo "strings match"
else
    echo "strings don't match"
fi
```

### Numbers and Arithmetic

```bash
# wrong! prints 31+21
echo 31+21

n1=4
n2=20
# print 24
echo $(( n1+n2 ))
echo $(expr $n1 + $n2)
```

### Arrays

```bash
car=("BMW" "Toyota" "Honda")
# print all elements
echo "${car[@]}"
# print only the first element
echo "${car[0]}"
# print indices
echo "${!car[@]}"
# print lenght of the array
echo "${#car[@]}"
# delete Toyota from the array
unset car[1]
# set index 2 to Mercedes
car[2]="Mercedes"
```

### Functions

```bash
function func()
{
    echo "this is a function"
}
# print this is a function
func

function funcPrint()
{
    echo $1
}
# print Hi
funcPrint Hi

function funcCheck()
{
    retValue="I love Linux"
}
retValue="I love MAC"
# print I love Mac
echo $retValue
funcCheck
# print I love Linux - funcCHeck overwrote retValue
echo $retValue
```

### Files and directories

```bash
# -d checks if argument exists and is directory
if [ -d "MyFolder" ]
then
    echo "MyFolder exists"
fi

# -f checks if argument exists and is file
if [ -f "MyFile" ]
then
    echo "MyFile exists"
fi
```

### Debugging

```bash
# execut script.sh in debug mode
bash -x ./script.sh

# debug only a section of the script
...
set -x
...
...
set +x
...
```

## Resources

### Articles

* [Shell Script Best Practices](https://sharats.me/posts/shell-script-best-practices/) - Shrikant Sharat Kandula

### Books

* [Advanced Bash-Scripting Guide](https://tldp.org/LDP/abs/html/) - Mendel Cooper

### GitHub repositories

* [pure bash bible](https://github.com/dylanaraps/pure-bash-bible)
* [pure sh bible](https://github.com/dylanaraps/pure-sh-bible)

### Websites

* [Shell Style Guide](https://google.github.io/styleguide/shellguide.html) - Google
