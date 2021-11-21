# Awk

```bash
# print entire file
awk '{print}' $filename

# print only lines containing windows
awk '/windows/ {print}' $filename

# print second and fourth fields from lines containing windows
awk '/windows/ {print $1,$3}' $filename

# using specific character as separator
printf '1:2:3' | awk -F ":" '{print $1}'

# sum integers from a file
printf '1\n2\n3\n' | awk '{sum += 1} END {print sum}'
```
