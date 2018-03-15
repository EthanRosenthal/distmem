# Table of Contents

- [Print Specific Lines of a File](#print-specific-lines-of-a-file)
- [grep with Line Number](#grep-with-line-number)

## Print Specific Lines of a File

Ex: printing lines 100-110 of `file.txt`

```bash
sed -n '100,110p' file.txt
```

## grep with Line Number

```bash
grep -n "where does this appear" file.txt
```
