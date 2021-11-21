# Sed

```bash
# replace the first i with I (s = substitute)
sed 's/i/I/' filename.txt

# subtitutes all i with I (g = global)
sed 's/i/I/g' filename.txt

# save result in newfile.txt - filename.txt is unmodified
sed 's/i/I/g' filename.txt > newfile.txt

# modifies filename.txt in place
sed -i 's/i/I/g' filename.txt

# using / for separating sections is just a convention
# any character will work
sed -i 's|i|I|g' filename.txt

# remove empty lines (d = delete)
sed '/^$/d' filename.txt

# insert before a matching pattern (i = insert)
sed '/Once upon a time/i\Chapter 1'

# insert after a matching pattern (a = append)
sed '/happily ever after/a\The end.'

# print the first 11 lines of filename.txt
sed 11q filename.txt

# apply multiple find-replace expressions
sed -e 's/find/replace' -e 's/find/replace' filename.txt
```
