# vagrant-hyperv

## VM + Cisco AnyConnect VPN

The secret to getting the VM to be able to access resources on the VPN is to follow the instructions in these two articles (configuring the internal network adapter with the instructions in the second article before sharing it with the AnyConnect interface):

References

- https://win10.guru/hyper-v-virtual-machine-use-host-vpn-connection/
- https://anandthearchitect.com/2018/01/06/windows-10-how-to-setup-nat-network-for-hyper-v-guests/

## SMB Mounts

The big pain in the arse...

SMB doesn't seem to work if your admin account is a domain joined account (or at least doesn't work in the case of my workplace).

The work around is to create a local user account, and most importantly, give that local account permissions for the specific shared folders you plan to use:

Right click -> Security -> explicitly add local user account with full control permissions.

Reference:

- https://issueexplorer.com/issue/hashicorp/vagrant/12425
