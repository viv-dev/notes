# jenkins

# Windows Agent

When running Windows Build machines that need to have Desktop access (e.g. testing graphical applications), the agent needs to be run from a powershell script on startup. The user running the agent also needs to have auto-login enabled.

The following powershell script grants a cuser auto-login capabilities:

```powershell
$RegPath = "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
$DefaultUsername = "<username>"
$DefaultPassword = "<password>"
Set-ItemProperty $RegPath "AutoAdminLogon" -Value "1" -type String
Set-ItemProperty $RegPath "DefaultUsername" -Value "$DefaultUsername" -type String
Set-ItemProperty $RegPath "DefaultPassword" -Value "$DefaultPassword" -type String
```

Then the following script can be placed in `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp`.

```powershell
cd c:\jenkins
powershell -ExecutionPolicy Bypass -File c:\jenkins\agent.ps1
```

With `c:\jenkins\agent.ps1` containing:

```powershell
$server = "<jenkins-server-url>"
$secret = "<secret>"
$hostname = (hostname).toLower()

# Make sure server is available
$netstatus = test-connection $server -quiet
while ( $netstatus -eq $false ) {
	start-sleep -s 5
	$netstatus = test-connection $server -quiet
}

# Get latest agent.jar
$wc = New-Object Net.WebClient
$wc.DownloadFile("http://$server/jnlpJars/agent.jar", 'c:\jenkins\agent.jar')

# Start agent
java -jar agent.jar -jnlpUrl "http://$server/computer/$hostname/jenkins-agent.jnlp" -secret $secret -workDir "c:/jenkins"
```

> NOTE: Replace with swarm.jar instead in a swarm setup
