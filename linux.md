# Linux

## Users and Permissions
#### List Users

```bash
# All users details
cat /etc/passwd

# Just the username
awk -F: '{ print $1}' /etc/passwd
```

#### See Groups User is in

```bash
# Your own groups
groups

# Another users groups
groups <username>
```

#### Delete User

```bash
userdel <username>    # Deletes the user, leaving home directory intact
userdel -r <username> # Deletes the user + home directory
```

