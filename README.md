# Group Managed Service Accounts Explorations
Contains the steps I took to test group managed service accounts on my very own machines.

## Terraform?
No, sorry. It would be nice and cheaper, but I want to get something working
quite fast. So I'm just jotting down the steps.

## Setting up DC
A new Windows Server 2019 instance. See [DCDeploymentConfigTemplate.xml](DCDeploymentConfigTemplate.xml) for the configuration options (basically, Active Directory Services and DNS).

Here is the script to promote the domain controller:

	Import-Module ADDSDeployment
	Install-ADDSForest `
	-CreateDnsDelegation:$false `
	-DatabasePath "C:\windows\NTDS" `
	-DomainMode "WinThreshold" `
	-DomainName "sebug.local" `
	-DomainNetbiosName "SEBUG" `
	-ForestMode "WinThreshold" `
	-InstallDns:$true `
	-LogPath "C:\windows\NTDS" `
	-NoRebootOnCompletion:$false `
	-SysvolPath "C:\windows\SYSVOL" `
	-Force:$true

## Setting up the web servers
Add two Windows Servers and join them to the domain. For that you may have to set the DNS address to the internal IP address of the DC.

Control Panel\Network and Internet\Network and Sharing Center

vEthernet (nat)

Properties

TCP/IPv4 -> Preferred DNS server


Then you should be able to ping sebug.local

Now you can simply

	Add-Computer -DomainName "sebug.local" -restart



