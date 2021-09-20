# powershell

## PowerShell Course

[PowerShell For Beginners Full Course](https://www.youtube.com/watch?v=UVUd9_k9C6A))

## Purpose of Powershell

- Improved management and automation
- Manage real-time
- Manages large scale

## Versions

To show version in the shell:

```powershell
(Get-Host).Version
```

Latest version as of June 2021, latest version is 7, default installed on Windows is 5.1.

## Aliases

dir / ls is an alias for `Get-ChildItem`.

You can get the underlying command for an alias by using `Get-Alias <alias>`. You can also see the entire list of aliases by typing `Get-Alias` with no arguments.

## File System

Every item in a file system is an 'object' and can be manipulated in PowerShell using common object properties.

Other things treated as 'objects':

- Registry
- Certificate store
- Active Directory
- RDS - remote desktop services
- IIS - internet information services

`Get-PSDrive` shows a list of 'drives' and many of the above might be listed here as things that can be navigated like a file system.

```powershell
cd env:
ls
```

This will list all of the environment variables, for example.

## SSH Commands

```powershell
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh vagrant@192.168.10.10 "cat >> .ssh/authorized_keys"
```
