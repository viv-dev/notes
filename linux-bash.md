# bash

## Modify the Behaviour of the Shell

You can set shell options at the top of a script file in order to modify the behaviour the shell. See [here](https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html) for a comprehensive list of options, but some of the most common/useful are as follows:

```bash
set -u # Causes unset variables to trigger an error, instead of being treated as just an empty variable
set -e # Exit if any command results in an error or non-zero return code
set -o pipefail # Fail an entire pipeline if a single command fails
```

## Recommended Start of All Scripts

```bash
#! /bin/bash

set -eu
set -o pipefail

# Save global script args
ARGS=("$@")

```

