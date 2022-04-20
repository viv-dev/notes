# perforce

## Terminology

`Depot` - Top level folder

`Codeline` - this is somewhat synonomous with a 'branch' in the git sense and is the evolution of a particular depot.

`Branch` - in perforce, a branch as created by the `p4 branch`command is a view that may or may not be used in branching operations. This can be confusing.

`Label` - likewise with branch, this is a confusing name as while the name implies it might operate like a git tag. "Instead, it creates or updates a Perforce database object called a label that defines a label name and a set of files to which the label may be applied."

`Workspace` - a 'view' into a depot, can be a sub path or an aggregation of subpaths. Originally, these were called `clients`.

`View` - is a scope of files that can be affected by an operation.

## Commands

```bash

# Add file to source control
p4 add

# Flag a file in source control for editing - this starts a changelist
p4 edit

# Submit edit files - this submits a changelist
p4 submit

# Download latest revision of files
p4 sync / p4 get (undocumented alias)

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
