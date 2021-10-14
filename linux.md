# linux

## List Users

```bash
# All details
cat /etc/passwd

# Just the username
awk -F: '{ print $1}' /etc/passwd
```

## See Groups User is in

```bash
# Your own groups
groups

# Another user
groups <username>
```
