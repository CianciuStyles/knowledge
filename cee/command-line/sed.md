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
```
