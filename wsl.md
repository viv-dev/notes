# WSL2

## VPN Connection

When connecting via Cisco AnyConnect the WSL2 environment loses internet connectivity.

[This](https://gist.github.com/machuu/7663aa653828d81efbc2aaad6e3b1431) git hub gist explains the work around

```powershell
Get-NetAdapter | Where-Object {$_.InterfaceDescription -Match "Cisco AnyConnect"} | Set-NetIPInterface -InterfaceMetric 6000
```

Made a script with a permenant alias based on info [here](https://stackoverflow.com/questions/24914589/how-to-create-permanent-powershell-aliases)

## Windows Environment Variables

WSL by default likes to load in your Windows user `path` into your WSL environment... this is super annoying as it then tries to use python or any other application you've added to your Windows path in your WSL environment. Since I prefer to keep my Windows and Linux utilities seperate (with the exception being Docker and VSCode), I prefer to disable it.

To do so, create the file `/etc/wsl.conf` in your WSL environment and add the following:

```ini
[interop]
# Stop windows path variables being exposed in wsl (so annoying...)
appendWindowsPath = false
```

See [here](https://devblogs.microsoft.com/commandline/automatically-configuring-wsl/) for more information.

If you do need access to certain Windows applications (like `docker` and `code`), I find it better to manually specify in your shell rc script (i.e `.bashrc` or `.zshrc`) so you have more explicit control:

```bash
# Add docker and vscode from the windows environment to the WSL path
export PATH=$PATH:"/mnt/c/Program Files/Docker/Docker/resources/bin":"/mnt/c/ProgramData/DockerDesktop/version-bin":"/mnt/c/Users/v_capote/AppData/Local/Programs/Microsoft VS Code/bin"
```