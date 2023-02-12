# Vagrant hyper-v

This is an example of using vagrant with the hyper-v provider. This will create 2 virtual machines based on generic/ubuntu2204 box with 10.98.0.xx/24 addresses.

See :
[vagrant documentation](https://developer.hashicorp.com/vagrant/docs/)
[hyper-v guides](https://learn.microsoft.com/en-us/iis/web-hosting/installing-infrastructure-components/hyper-v-guides)
[generic/ubuntu2204 box](https://app.vagrantup.com/generic/boxes/ubuntu2204)

# Quickstart

## Prerequisite 

### Install Hyper-v

Open a PowerShell console as Administrator and run following command

``` powershell
DISM /Online /Enable-Feature /All /FeatureName:Microsoft-Hyper-V
```

### Install vagrant

[vagrant installation](https://developer.hashicorp.com/vagrant/docs/installation)

Reboot the operating system.


## Configure Hyper-v network

[microsoft documentation](https://learn.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/setup-nat-network)

1. Open a PowerShell console as Administrator.

2. Create an internal switch
``` powershell
New-VMSwitch -SwitchName "Lan 98" -SwitchType Internal
```

3. Find interface index
``` powershell
Get-NetAdapter
```

Your output should look someting like this:
``` text
Name                  InterfaceDescription               ifIndex Status       MacAddress           LinkSpeed
----                  --------------------               ------- ------       ----------           ---------
vEthernet (Lan 98)    Hyper-V Virtual Ethernet Adapter #3     76 Up           00-15-5D-01-2B-28      10 Gbps
Wi-Fi                 Marvell AVASTAR Wireless-AC Net...      18 Up           98-5F-D3-34-0C-D3     300 Mbps
Bluetooth Network ... Bluetooth Device (Personal Area...      21 Disconnected 98-5F-D3-34-0C-D4       3 Mbp
```
Take note of ifIndex for the internal switch created

4. Configure NAT gateway using New-NetIPAddress

Here is the generic commande
``` powershell
New-NetIPAddress -IPAddress <ip address> -PrefixLength <prefix length> -InterfaceIndex <interface index>
```

In order to configure the gateway for our previously created internal switch, run the following command
``` powershell
New-NetIPAddress -IPAddress 10.98.0.1 -PrefixLength 24 -InterfaceIndex 76
```

5. Configure new NAT networking

Here is the generic commande
``` powershell
New-NetNat -Name <NAT outside name> -InternalIPInterfaceAddressPrefix <NAT subnet prefix>
```

For ou configuration run the following command
``` powershell
New-NetNat -Name "Nat 98" -InternalIPInterfaceAddressPrefix 10.98.0.0/24
```

Reboot the operating system.

## Generate SSH Keys

In order to connect to hyper-v virtual machines, generate a new pair of ssh keys with the following command
``` powershell
ssh-keygen -t ed25519
```

## Vagrant commands

Use this command to create virtual machines
``` powershell
vagrant up
```

use this command to connect to node1
``` powershell
vagrant ssh node1
```

Use this command to remove created virtual machines
> :warning: This will remove all virtual machines
``` powershell
vagrant destroy
```
