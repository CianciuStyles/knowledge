# Grep

```bash
# print lines containing hello in file.txt
grep hello file.txt

# case insensitive search
grep -i hello file.txt

# print line number of matches
grep -n hello file.txt

# print number of matches
grep -c hello file.txt

# print lines not containing hello in file.txt
grep -v hello file.txt

# search hello recursively in dir
grep -R hello dir

# ignore non-text files
grep -I hello dir

# print 3 line of [C]ontext around, [A]fter or [B]efore a match
grep -C|B|A 3 hello file.txt
```
