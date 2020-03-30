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

## Giving rights
I have created an account that is supposed to be admin of the ws-machines. I
created a group "Service Administrators" and put him in it.

Then opening group policy management.

Adding a local admin GPO.

https://social.technet.microsoft.com/wiki/contents/articles/7833.how-to-make-a-domain-user-the-local-administrator-for-all-pcs.aspx

## Installing the IIS Role
Using the newly created service administrator account, add the IIS role.

Once you have set up the configuration on the first machine, you can reuse the same configuration on the second - in an admin PowerShell:

	Install-WindowsFeature -ConfigurationFilePath .\WSDeploymentConfigTemplate.xml

## Group Managed Service accounts

	Add-KDSRootKey -effectiveimmediately

Created a gmsa1Group that will contain the systems that will be allowed to retrieve the password.

	New-ADServiceAccount -Name gmsa1 -DNSHostName service1.sebug.local -PrincipalsAllowedToRetrieveManagedPassword gmsa1Group

Also set the

	Set-ADServiceAccount gmsa1 -PrincipalsAllowedToRetrieveManagedPassword ws1$, ws2$

Restart the machines.

Then, using the Active Directory module, on each machine:

	Install-ADServiceAccount gmsa1

You can then start using them for, say, app pools, by specifying

SEBUG\gmsa1$

as the user account.


