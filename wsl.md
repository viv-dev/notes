# wsl.md

## Windows Environment Variables

WSL by default likes to load in your user path into your WSL environment... this is super annoying as it then tries to use python or any other application you've added to your path.

To disable this behaviour, create the file `/etc/wsl.conf` and add the following:

```ini
[interop]
# Stop windows path variables being exposed in wsl (so annoying...)
appendWindowsPath = false
```

See [here](https://devblogs.microsoft.com/commandline/automatically-configuring-wsl/) for more information.

If you do need access to certain applications (e.g. `docker` and `code` is useful to retain access to), I find it better to manually specify in your shell rc script (i.e `.bashrc` or `.zshrc`) so you have more explicit control.