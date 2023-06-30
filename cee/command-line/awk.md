# Awk

```bash
# print entire file
awk '{print}' $filename

# print only lines containing windows
awk '/windows/ {print}' $filename

# print second and fourth fields from lines containing windows
awk '/windows/ {print $1,$3}' $filename

# print the last column of each line in a file
awk '{print $NF}' $filename

# print every third line starting from the first line
awk 'NR%3==1' $filename

# using specific character as separator
printf '1:2:3' | awk -F ":" '{print $1}'

# sum integers from a file
printf '1\n2\n3\n' | awk '{sum += 1} END {print sum}'
```

## Resources

### Books

* [The AWK Programming Language (Second Edition)](https://awk.dev/)
