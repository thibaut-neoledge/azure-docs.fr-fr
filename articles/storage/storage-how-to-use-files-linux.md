---
title: "Utilisation d’Azure Files avec Linux | Microsoft Docs"
description: "Créez un partage de fichiers Azure dans le cloud avec ce didacticiel pas à pas. Gérez le contenu de votre partage de fichier et montez un partage de fichiers à partir d’une machine virtuelle Azure exécutée sous Linux ou d’une application locale prenant en charge SMB 3.0."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/18/2017
ms.author: renash
translationtype: Human Translation
ms.sourcegitcommit: e296e468309b53338231e283ac62e4d917e0834b
ms.openlocfilehash: 8cb98eb721d5769125926a6c75f776a9d510376e


---
# <a name="how-to-use-azure-file-storage-with-linux"></a>Utilisation du stockage de fichiers Azure avec Linux
## <a name="overview"></a>Vue d'ensemble
Le stockage de fichiers Azure propose des partages de fichiers dans le cloud s’appuyant sur le protocole SMB standard. Avec Azure Files, vous pouvez migrer des applications d’entreprise qui s’appuient sur des serveurs de fichiers dans Azure. Les applications exécutées dans Azure permettent de monter facilement des partages de fichiers à partir de machines virtuelles Azure exécutées sous Linux. Et avec la dernière version du stockage de fichiers, vous pouvez également monter un partage de fichiers à partir d’une application locale prenant en charge SMB 3.0.

Vous pouvez créer des partages de fichiers Azure à l’aide du [portail Azure](https://portal.azure.com), des applets de commande Azure Storage PowerShell, des bibliothèques clientes Azure Storage ou de l’API REST d’Azure Storage. En outre, étant donné que les partages de fichiers sont des partages SMB, vous pouvez y accéder via les API de système de fichiers standard.

File Storage repose sur la même technologie que Blob Storage, Table Storage et Queue Storage : il offre donc la disponibilité, la durabilité, l’évolutivité et la redondance géographique intégrées à la plateforme de stockage Azure. Pour plus d’informations sur les objectifs et les limites des performances de File Storage, consultez [Objectifs d’évolutivité et de performances d’Azure Storage](storage-scalability-targets.md).

Le stockage de fichiers est maintenant mis à la disposition générale et prend en charge SMB 2.1 et SMB 3.0. Pour plus d’informations sur File Storage, consultez l’ [API REST du service de fichiers](https://msdn.microsoft.com/library/azure/dn167006.aspx).

> [!NOTE]
> Dans la mesure où le client SMB Linux ne prend pas encore en charge le chiffrement, le montage d’un partage de fichiers à partir de Linux requiert toujours que le client soit situé dans la même région Azure que le partage de fichiers. Toutefois, la prise en charge du chiffrement pour Linux est prévu par les développeurs Linux responsables de la fonctionnalité SMB. Les distributions Linux qui prendront en charge le chiffrement à l’avenir seront en mesure de monter un partage de fichiers Azure à partir de n’importe quel endroit.
> 
> 

## <a name="video-using-azure-file-storage-with-linux"></a>Vidéo : Utilisation du stockage de fichiers Azure avec Linux
Voici une vidéo montrant comment créer et utiliser des partages de fichiers Azure sous Linux.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-File-Storage-with-Linux/player]
> 
> 

## <a name="choose-a-linux-distribution-to-use"></a>Sélection d'une distribution Linux à utiliser
Lorsque vous créez une machine virtuelle Linux dans Azure, vous pouvez spécifier une image Linux qui prend en charge SMB 2.1 ou version ultérieure à partir de la galerie d’images Azure. Voici une liste d’images Linux recommandées :

* Ubuntu Server 14.04+
* RHEL 7+
* CentOS 7+
* Debian 8
* openSUSE 13.2+
* SUSE Linux Enterprise Server 12
* SUSE Linux Enterprise Server 12 (Image Premium)

## <a name="mount-the-file-share"></a>Montage du partage de fichiers
Pour monter le partage de fichiers à partir d’une machine virtuelle exécutant Linux, vous devrez peut-être installer un client SMB/CIFS si la distribution que vous utilisez n’a pas un client intégré. Il s’agit de la commande Ubuntu pour installer une sélection cifs-utils :

```
sudo apt-get install cifs-utils
```

Ensuite, vous devez effectuer point de montage (mkdir mymountpoint) avant d’émettre la commande de montage similaire à ceci :

```
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Vous pouvez également ajouter des paramètres dans votre répertoire /etc/fstab pour monter le partage.

Veuillez noter que 0777 représente un code d’autorisation de fichier/répertoire qui octroie des autorisations d'exécution, de lecture ou d'écriture à tous les utilisateurs. Vous pouvez le remplacer avec un autre code d’autorisation de fichier en fonction du document d’autorisation de fichier Linux.

De même, pour conserver le montage d’un partage de fichiers après le redémarrage, vous pouvez ajouter un paramètre comme ci-dessous dans votre répertoire /etc/fstab :

```
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Par exemple, si vous avez créé une machine virtuelle Azure à l'aide de l'image Linux Ubuntu Server 15.04 (disponible dans la galerie d'images Azure), vous pouvez monter le fichier comme indiqué ci-dessous :

```
azureuser@azureconubuntu:~$ sudo apt-get install cifs-utils
azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
Filesystem  Size  Used Avail Use% Mounted on
//myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint
```

Si vous utilisez CentOS 7.1, vous pouvez monter le fichier comme indiqué ci-dessous :

```
[azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
[azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
[azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
Filesystem  Size  Used Avail Use% Mounted on
//myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint
```

Si vous utilisez Open SUSE 13.2, vous pouvez monter le fichier comme indiqué ci-dessous :

```
azureuser@AzureconSuse:~> sudo zypper install samba*  
azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
azureuser@AzureconSuse:~> df -h /mnt/mountpoint
Filesystem  Size  Used Avail Use% Mounted on
//myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint
```

Si vous utilisez RHEL 7.3, vous pouvez monter le fichier comme indiqué ci-dessous :

```
azureuser@AzureconRedhat:~> sudo yum install cifs-utils
azureuser@AzureconRedhat:~> sudo mkdir /mnt/mountpoint
azureuser@AzureconRedhat:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
azureuser@AzureconRedhat:~> df -h /mnt/mountpoint
Filesystem  Size  Used Avail Use% Mounted on
//myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint
```

## <a name="manage-the-file-share"></a>Gestion du partage de fichiers
Le [portail Azure](https://portal.azure.com) offre une interface utilisateur permettant de gérer Azure File Storage. Vous pouvez effectuer les actions suivantes à partir de votre navigateur web :

* Charger et télécharger des fichiers vers et à partir de votre partage de fichiers.
* Surveiller l'utilisation réelle de chaque partage de fichiers.
* Ajuster le quota de la taille de partage.
* Copiez la commande `net use` à utiliser pour monter le partage de fichiers à partir d’un client Windows.

Vous pouvez également utiliser l'interface de ligne de commande Azure (Azure CLI) interplateforme à partir de Linux pour gérer le partage de fichiers. L'interface de ligne de commande Azure offre un ensemble de commandes open source, interplateforme, vous permettant d'utiliser le stockage Azure, y compris le stockage de fichiers. Elle offre des fonctionnalités similaires à celles du portail Azure, ainsi que des fonctions étendues d'accès aux données. Pour obtenir des exemples, consultez [Utilisation de la CLI Azure avec Azure Storage](storage-azure-cli.md).

## <a name="develop-with-file-storage"></a>Développement avec le stockage de fichiers
En tant que développeur, vous pouvez créer une application avec le stockage de fichiers à l’aide de la [bibliothèque cliente Azure Storage pour Java](https://github.com/azure/azure-storage-java). Pour obtenir des exemples de code, consultez [Utilisation du stockage de fichiers à partir de Java](storage-java-how-to-use-file-storage.md).

Vous pouvez également utiliser la [bibliothèque cliente Azure Storage pour Node.js](https://github.com/Azure/azure-storage-node) pour développer sur File Storage.

## <a name="feedback-and-more-information"></a>Commentaires et informations supplémentaires
Utilisateurs de Linux, nous attendons votre avis !

Le stockage de fichiers Azure pour le groupe d'utilisateurs Linux propose un forum vous permettant de partager vos commentaires lorsque vous évaluez et adoptez le stockage de fichiers sur Linux. Envoyez un courrier électronique aux [utilisateurs Linux d’Azure File Storage](mailto:azurefileslinuxusers@microsoft.com) pour rejoindre le groupe d’utilisateurs.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le stockage de fichiers Azure, consultez ces liens.

### <a name="conceptual-articles-and-videos"></a>Vidéos et articles conceptuels
* [Stockage de fichiers Azure : un système de fichiers SMB dans le cloud sans friction pour Windows et Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Prise en main d’Azure File Storage sur Windows](storage-dotnet-how-to-use-files.md)

### <a name="tooling-support-for-file-storage"></a>Outils pris en charge pour le stockage de fichiers
* [Transfert de données avec l'utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
* [Création et gestion de partages de fichiers](storage-azure-cli.md#create-and-manage-file-shares) à l’aide de l’interface CLI Azure

### <a name="reference"></a>Référence
* [Référence de l’API REST du service de fichiers](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Article sur la résolution des problèmes des fichiers Azure](storage-troubleshoot-file-connection-problems.md)

### <a name="blog-posts"></a>Billets de blog :
* [Le stockage de fichiers Azure est désormais mis à la disposition générale](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Stockage de fichiers dans Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Présentation de Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Conservation des connexions vers les fichiers Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)



<!--HONumber=Jan17_HO3-->


