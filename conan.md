# conan-create-profile

```bash
conan profile new <mynewprofile> --detect
```

# Conan With Pyenv on Windows

In order to be able to find the path where conan was installed by pyenv, on Windows you can run the following PowerShell command:

```powershell
gci -r -fi  conan.exe
```
