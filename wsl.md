# WSL2

## VPN Connection

When connecting via Cisco AnyConnect the WSL2 environment loses internet connectivity.

[This](https://gist.github.com/machuu/7663aa653828d81efbc2aaad6e3b1431) git hub gist explains the work around

```powershell
Get-NetAdapter | Where-Object {$_.InterfaceDescription -Match "Cisco AnyConnect"} | Set-NetIPInterface -InterfaceMetric 6000
```

Made a script with a permenant alias based on info [here](https://stackoverflow.com/questions/24914589/how-to-create-permanent-powershell-aliases)

For convenience, it is useful to make an alias in the appropriate `profile.ps1` for the verison of Powershell you are using:

```powershell
Set-Alias fix-wsl2 "<path-to-anyconnect-fix-script>"
```

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

## Mount Network Drive In WSL

First make sure that a directory for the mount exists. For example, if you would like to map the 'G:' drive from Windows into WSL, you would do the following:

```bash
sudo mkdir /mnt/g
mount -t drvfs G: /mnt/g
```

If you would like to make the mount permanent, add an entry for it to your `/etc/fstab` file:

```bash
G: /mnt/g drvfs defaults 0 0
```

## Enable Routing to VMs in Other Virutal Subnets

In order to access machines running in vms connected to a different virtual switch (e.g. running ansible on a test of vagrant test machines) you need to enable the following Windows option in the registry:

```powershell
 Set-ItemProperty -Path HKLM:\system\CurrentControlSet\services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
