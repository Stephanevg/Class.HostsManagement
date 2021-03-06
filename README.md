# Manage local and remote Hosts file(s) using Powershell.

This modules helps to read, parse, save, edit, and backup your local or remote HOSTS file.
The main advantage of this module is that it converts the HOSTS file into a rich object.

It becomes easy to filter a HOSTS file to find entries that are double, incorrect, or that need to be deleted.
New entries can easily be added as well.
Alll this at scale, on hundred machines from your network at once, from one single locatino.

# Getting Started

## getting and analyzing Hosts file information

The following command will list the content of the local hosts file

```powershell
 Get-HFMHostsfile | Get-HFMHostsFileContent
```

###How to get Hosts File information from a remote computer?

```powershell
Get-HFMHostsfile -ComputerName $RemoteComputerName | Get-HFMHostsFileContent | ft -AutoSize
```

## Understanding the data:


## Adding data

First, get a reference to the HostsFile you would like to manipulate:

```powershell
$HostsFile = Get-HFMHostsfile # Gets the local hostsfile .
```

```powershell
$NewEntry = New-HFMHostsFileEntry -IpAddress "10.20.30.40" -HostName "StephaneVG" -FullyQualifiedName "Stephane.v.g" -Description "My machine" -EntryType Entry
```

Looking in the `$NewEntry` variable, a new HostsFile entry is prepared:

```powershell
>  $NewEntry


Ipaddress          : 10.20.30.40
HostName           : StephaneVG
FullQuallifiedName : Stephane.v.g
Description        : My machine
EntryType          : Entry
```

Now that we hold a reference to our HostsFile where we want to add the data (`$HostsFile`) and a reference to the data we actually want to add (`$NewEntry`) we can add the data using the following line:

```powershell
Set-HFMHostsFileEntry -Path $HostsFile -Entries $NewEntry
```

Looking in the hostsfile, there will be a new line:

```
10.20.30.40		StephaneVG		Stephane.v.g		# My machine
```

## Backup mechanism

The HostsFile is a crucial component for a system that cannot rely on DNS. Messing up the HostsFile can have some serious consequences on the ability to reach other members of the network. Therefor, each time `Set-HFMHostsEntry` is called a **backup** of the `hosts` file is automatically done.
The backup is located in the same folder as the HostsFile. Here under is the file generated by our previous command:

`20181118-104429_Hosts.bak`

The mechanism goes up until 5 backups max. After that, it will delete the oldest one in order to be able to add a more recent one.

## Trigger a backup

Sometimes, one doesn't want to wait until the we change something to the hosts file in order to trigger a backup. Scheduling backups of local hosts and saving them to a central location is possible using the `Save-HFMHostsFile` cmdlet.

```powershell
Get-FHMHostsFile | Save-HFMHostFile -BackupFolder \\Server01\HostsFileBackups\
```

## Read my blog post about implementation details:

If you are interested in the details of this module, want to learn about it's history, and improve your knowledge about powershell classes, I have writen an extensive documentation on my blog. You can find it on [PowerShellDistrict-ManagingHostsFile](http://powershelldistrict.com/managing-hosts-file-using-powershell-classes/)


--------------------------

# Contributing

For the ones that would like to contribute, it is possbile to find technical details on the implementation of the module [here](./ModuleStructure.md)

