# vagrant-hyperv

## Install Required Vagrant Plugins

```bash
vagrant plugin install vagrant-reload
vagrant plugin install vagrant-env
```

## VM + Cisco AnyConnect VPN

In order to get Hyper-V switches to access the internet via a Cisco AnyConnect VPN network, you will need to create a dedicated Nat Switch. This can be created with the following powershell script:

```powershell
# See: https://www.petri.com/using-nat-virtual-switch-hyper-v
$NATSwitchName = "VPN-NAT-Switch"
$NATNetworkName = "VPNShareNat"
$NetworkAddress = 192.168.200.0.24
$NetworkGateway = 192.168.200.1

if ($NATNetworkName -in (Get-NetNat | Select-Object -ExpandProperty Name) -eq $FALSE) {
    New-NetNAT -Name $NATNetworkName -InternalIPInterfaceAddressPrefix $NetworkAddress
} else {
    
    "$NATNetworkName NAT Network already exists; skipping"
}

If ($NATSwitchName -in (Get-VMSwitch | Select-Object -ExpandProperty Name) -eq $FALSE) {
    "Creating Internal-only switch named $NATSwitchName on Windows Hyper-V host..."

    New-VMSwitch -SwitchName $NATSwitchName -SwitchType Internal
    New-NetIPAddress -IPAddress $NetworkGateway -PrefixLength 24 -InterfaceAlias "vEthernet ($NATSwitchName)"
}
else {
    "$NATSwitchName for static IP configuration already exists; skipping"
}
```

You then need to configure the Cisco Anyconnect Windows adapter to share it's connection with the `VNP-Nat-Switch` you created. Instructions can be found here:

https://win0.guru/hyper-v-virtual-machine-use-host-vpn-connection/

When it comes to configuring Vagrant VMs to use this switch, it needs to be done in a two stage process. The first is to initialise the VM normally using the `Default Switch` (so be sure to select it at the prompt after running `vagrant up`), then configure a provisioning step to set the static IP address for the VM within the `192.168.200.0/24` network of the `VPN-Nat-Switch`, and lastly is to change the VM to use the `VPN-Nat-Switch`.

To do this last pert we use the `vagrant-reload` plugin in order to add a hook to run a script that changes the switch the VM. 

## SMB Mounts

In order to be able to mount SMB mounts with the correct permissions you need a user account with Admin permissions (local or domain joined). You then need to ensure the user you're using to create the SMB mount has the correct permissions on the folder you want to share.

To do this, on the directory you want to share right click and then select Properties -> Security. In this tab, the top level has a list of group/user names that can access this directory - click `Edit`.

In the pop-up window that opens, then click the `Add..` button. In the box that pops up, add the admin account you want to use. In the second pop-up window, click on the admin account you just added and in the bottom panel enable `Full control`for this user. Once done, click `OK`.

Finally, in the last window, double check the summary for the admin account has full control and then press `OK` in this window too.

To avoid having to constantly type in your password everytime you start/restart, you can add your username and password to the shell session you are using as environment variables (e.g. `SMB_USERNAME` and `SMB_PASSWORD` as per the snippet below).

Lastly, in your `Vagrantfile` to correctly mount the samba share you can use the following settings:

```ruby
## Mounting in Linux VMs - Local User
lin.vm.synced_folder  "./", "/vagrant",
type: "smb",
smb_username: ENV['SMB_USERNAME'],
smb_password: ENV['SMB_PASSWORD'],
mount_options: ["sec=ntlmv2", "file_mode=0777","dir_mode=0777","vers=3.0","noperm"] 

## Mounting in windows VMs
win.vm.synced_folder   "./", "/vagrant",
type: "smb",
smb_username: ENV['SMB_USERNAME'],
smb_password: ENV['SMB_PASSWORD']

## Mounting in Linux VMs
lin.vm.synced_folder  "./", "/vagrant",
type: "smb",
smb_username: ENV['SMB_USERNAME'],
smb_password: ENV['SMB_PASSWORD'],
mount_options: ["dom=<domain-name>","sec=ntlmv2", "file_mode=0777","dir_mode=0777","vers=3.0","noperm"] 

## Mounting in windows VMs - Domain Joined User
win.vm.synced_folder   "./", "/vagrant",
type: "smb",
smb_username: ENV['SMB_USERNAME'],
smb_password: ENV['SMB_PASSWORD'],
mount_options: ["dom=<domain-name>"]
```

## Example Vagrantfile

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Enable loading of environment variables from file
  config.env.enable

  ############################################
  # VPN-NAT-Switch Trigger
  ############################################
  # Before reload during provisioning, run this trigger to change which switch the vms are using
  config.trigger.before :reload do |trigger|
    trigger.info = "Setting Hyper-V switch to 'VPN-NAT-Switch' to allow for VPN access..."

    trigger.run = {privileged: "true", powershell_elevated_interactive: "true", path: "scripts/set-hyperv-switch.ps1"}
  end

  #############################################
  # Linux VM example
  #############################################
  config.vm.define "lin", primary: true do |lin|
    # Box/provider
    lin.vm.box = "centos/7"
    lin.vm.provider "hyperv"

    # Network info
    lin.vm.hostname = "lin"

    # Set hyper-v settings
    lin.vm.provider :hyperv do |hyperv|
      hyperv.vmname = "lin"
      hyperv.enable_virtualization_extensions = true
      hyperv.linked_clone = true
      hyperv.memory = 8192
      hyperv.cpus = 4

    end

    # Mount required folders
    lin.vm.synced_folder "./", "/vagrant",
    type: "smb",
    smb_username: ENV['SMB_USERNAME'],
    smb_password: ENV['SMB_PASSWORD'],
    mount_options: ["dom=CORP","sec=ntlmv2", "file_mode=0777","dir_mode=0777","vers=3.0","noperm"]

    # Run linux provisioning scripts
    lin.vm.provision "shell", path: "scripts/centos-install-deps.sh"

    # Set a static ip on the VM
    lin.vm.provision "shell", path: "scripts/centos-static-ip.sh", args: 20 # Set static ip to 192.168.200.20

    # Restart the vm to trigger the switch trigger
    lin.vm.provision :reload
  end

  ############################################
  # Windows VM Example
  ############################################
  config.vm.define "win", primary: true do |win|

    # Box/provider
    win.vm.box = "gusztavvargadr/windows-10"
    win.vm.provider "hyperv"

    # Static ip that will be used
    win.vm.hostname = "ansible-win"

    # Set hyper-v vm name
    win.vm.provider :hyperv do |hyperv|
      hyperv.vmname = "win"
      hyperv.enable_virtualization_extensions = true
      hyperv.linked_clone = true
    end

    # Mount required folders
    win.vm.synced_folder  "./", "/vagrant",
      type: "smb",
      smb_username: ENV['WIN_SMB_USERNAME'],
      smb_password: ENV['SMB_PASSWORD'],
      mount_options: ["dom=CORP"]

    # Run windows provisioning steps
    win.vm.provision "shell", path: "scripts/win-install-deps.ps1"

    # Schedule changing of the static ip address next boot
    win.vm.provision "shell" do |s| 
      s.path = "scripts/win-schedule-static-ip.ps1"
      s.privileged = true
      s.args = "21" # Set static ip to 192.168.200.21
    end
    
    win.vm.provision :reload
  end
end
```

Source for `set-hyperv-switch.ps1`:
```powershell
Get-VM "win" | Get-VMNetworkAdapter | Connect-VMNetworkAdapter -SwitchName "VPN-NAT-Swtich"
Get-VM "lin" | Get-VMNetworkAdapter | Connect-VMNetworkAdapter -SwitchName "VPN-NAT-Swtich"
```

Source for `win-schedule-static-ip.ps1`:
```powershell
param ($host_octet)

$vagrantScriptPath = "C:\vagrant\scripts\win-set-static-ip.ps1"
$scriptPath = "C:\win-set-static-ip.ps1"

Copy-Item $vagrantScriptPath -Destination $scriptPath

$Trigger = New-ScheduledTaskTrigger -AtStartup
$Action = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-executionpolicy bypass -noprofile -file $scriptPath $host_octet" 
$Principal = New-ScheduledTaskPrincipal -UserID "NT AUTHORITY\SYSTEM" -LogonType ServiceAccount -RunLevel Highest

Register-ScheduledTask -TaskName SetStaticIP -Trigger $Trigger -Action $Action -Principal $Principal
```

Source for `win-set-static-ip.ps1` (**NOTE**: change the values `<vpn-dns-1>` and `<vpn-dns-2>`):
```powershell
param ($host_octet)

New-NetIPAddress -IPAddress "192.168.200.$host_octet" -DefaultGateway 192.168.200.1 -PrefixLength 24 -InterfaceIndex (Get-NetAdapter).InterfaceIndex
Set-DNSClientServerAddress -InterfaceIndex (Get-NetAdapter).InterfaceIndex -ServerAddresses <vpn-dns-1>,<vpn-dns-2>

Disable-ScheduledTask -TaskName SetStaticIP
```

Source for `lin-set-static-ip.sh` (**NOTE**: change the values `<vpn-dns-1>` and `<vpn-dns-2>`):
```bash
#!/bin/bash

echo "Octet: $1"

cat <<EOF >/etc/sysconfig/network-scripts/ifcfg-eth0
# static IP address on CentOS 7 or RHEL 7
NAME=eth0
DEVICE=eth0
ONBOOT=yes
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPADDR=192.168.200.${1}
PREFIX=24
GATEWAY=192.168.200.1
DNS1=<vpn-dns-1>
DNS2=<vpn-dns-1>

# Disable ipv6 #
IPV6INIT=no
EOF

```