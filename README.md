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



