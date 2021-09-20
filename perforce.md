# perforce

## Get list of changes

```bash
# Get all change logs (massive output dump)
p4 changes 

# Get the 3 latest changes
p4 changes -m 3 //codeline/path/...

# Get the 3 earliest changes
p4 changes -r -m 3 //codeline/path/...

# Get total number of changes
p4 changes //codeline/path/... | wc -l

# Get description of a particular changelist
p4 -Ztag -F %Description% change -o CHANGENUM

```

