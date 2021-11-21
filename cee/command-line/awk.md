# Awk

```bash
# print entire file
awk '{print}' $filename

# print only lines containing windows
awk '/windows/ {print}' $filename

# print second and fourth fields from lines containing windows
awk '/windows/ {print $1,$3}' $filename
```
