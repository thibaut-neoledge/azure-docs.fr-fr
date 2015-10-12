<properties
	pageTitle="Utilisation du stockage de fichiers Azure avec Linux | Microsoft Azure"
        description="Créez un partage de fichiers dans le cloud et montez-le à partir d’une machine virtuelle Azure ou une application locale en cours d’exécution sur Linux."
        services="storage"
        documentationCenter="na"
        authors="jutang"
        manager="jahogg"
        editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="09/28/2015"
      ms.author="jutang;tamram" />


# Utilisation du stockage de fichiers Azure avec Linux 

## Vue d'ensemble

Le stockage de fichiers Azure propose des partages de fichiers dans le cloud s’appuyant sur le protocole SMB standard. Le stockage de fichiers est maintenant mis à la disposition générale et prend en charge SMB 3.0 et SMB 2.1.

Vous pouvez créer des partages de fichiers Azure à l’aide du portail Azure en version préliminaire, des applets de commande Azure Storage PowerShell, des bibliothèques clientes Azure Storage ou de l’API REST d’Azure Storage. En outre, étant donné que les partages de fichiers sont des partages SMB, vous pouvez y accéder via les API de système de fichiers standard et connues.

Les applications exécutées dans Azure permettent de monter facilement des partages de fichiers à partir de machines virtuelles Azure. Et avec la dernière version du stockage de fichiers, vous pouvez également monter un partage de fichiers à partir d’une application locale prenant en charge SMB 3.0.

Notez que dans la mesure où le client SMB Linux ne prend pas encore en charge le chiffrement, le montage d’un partage de fichiers à partir de Linux requiert toujours que le client soit situé dans la même région Azure que le partage de fichiers. Toutefois, la prise en charge du chiffrement pour Linux est prévu par les développeurs Linux responsables de la fonctionnalité SMB. Les distributions Linux qui prendront en charge le chiffrement à l’avenir seront en mesure de monter un partage de fichiers Azure à partir de n’importe quel endroit.

## Distribution Linux à utiliser ##

Lorsque vous créez une machine virtuelle Linux dans Azure, vous pouvez spécifier une image Linux qui prend en charge SMB 2.1 ou version ultérieure à partir de la galerie d’images Azure. Voici une liste d’images Linux recommandées :

- Ubuntu Server 14.04	
- Ubuntu Server 15.04	
- CentOS 7.1+	
- Open SUSE 13.2	
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 (Image Premium)

## Montage du partage de fichiers ##

Pour monter le partage de fichiers à partir d’une machine virtuelle exécutant Linux, vous devrez peut-être installer un client SMB/CIFS si la distribution que vous utilisez n’a pas un client intégré. Il s’agit de la commande Ubuntu pour installer une sélection cifs-utils :

    sudo apt-get install cifs-utils

Ensuite, vous devez effectuer point de montage (mkdir mymountpoint) avant d’émettre la commande de montage similaire à ceci :

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Vous pouvez également ajouter des paramètres dans votre répertoire /etc/fstab pour monter le partage.

Veuillez noter qu’ici, 0777 représente un code d’autorisation de fichier/répertoire qui octroie des autorisations d’exécution, de lecture ou d’écriture à tous les utilisateurs. Vous pouvez le remplacer avec un autre code d’autorisation de fichier en fonction du document d’autorisation de fichier Linux.
 
De même, pour conserver le montage d’un partage de fichiers après le redémarrage, vous pouvez ajouter un paramètre comme ci-dessous dans votre répertoire /etc/fstab :

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username= myaccountname,password= StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Pour être plus précis, Voici un exemple.

Si vous avez créé une machine virtuelle Azure à l’aide de l’image Linux Ubuntu Server 15.04 disponible sur Azure Marketplace, vous pouvez monter le fichier comme indiqué ci-dessous :

    azureuser@azureconubuntu:~$ sudo apt-get install apt-file
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Si vous utilisez CentOS 7.1, vous pouvez monter le fichier comme indiqué ci-dessous :

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Si vous utilisez Open SUSE 13.2, vous pouvez monter le fichier comme indiqué ci-dessous :

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint


## Étapes suivantes

Pour plus d’informations sur le stockage de fichiers Azure, consultez ces liens.

### Articles conceptuels

- [Utilisation du stockage de fichiers Azure avec Windows](storage-dotnet-how-to-use-files.md)

### Outils pris en charge pour le stockage de fichiers

- [Utilisation de AzCopy avec Microsoft Azure Storage](storage-use-azcopy.md)
- [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](storage-azure-cli.md#create-and-manage-file-shares)

### Référence

- [Référence de l’API REST du service de fichiers](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Billets de blog :

- [Le stockage de fichiers Azure est désormais mis à la disposition générale](http://go.microsoft.com/fwlink/?LinkID=626728&clcid=0x409)
- [Présentation approfondie du stockage de fichiers Azure](http://go.microsoft.com/fwlink/?LinkID=626729&clcid=0x409) 
- [Présentation de Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Conservation des connexions vers les fichiers Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=Oct15_HO1-->