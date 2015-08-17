<properties
	pageTitle="Modification de la lettre de lecteur du disque temporaire Windows"
	description="Explique comment remapper le disque temporaire sur une machine virtuelle Windows dans Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="kathydav"/>

#Modification de la lettre de lecteur du disque temporaire Windows

Si vous avez besoin d'utiliser le lecteur D pour stocker des données, suivez ces instructions pour utiliser un autre lecteur de disque temporaire. N'utilisez jamais le disque temporaire pour stocker des données à conserver.

Avant de commencer, vous aurez besoin d'un disque de données attaché à la machine virtuelle afin d'y enregistrer le fichier d'échange Windows (pagefile.sys) au cours de cette procédure. Si vous n’en avez pas, consultez l’article [Association d’un disque de données à une machine virtuelle Windows][Attach]. Pour obtenir des instructions sur la recherche des disques associés, consultez « Recherche du disque » dans [Détachement d’un disque d’une machine virtuelle Windows][Detach].

Si vous souhaitez utiliser un disque de données existant sur le lecteur D, assurez-vous que vous avez également téléchargé le disque dur virtuel vers le compte de stockage. Pour plus d'instructions, consultez les étapes 3 et 4 de la rubrique [Création et téléchargement d'un disque dur virtuel Windows Server dans Azure][VHD].

> [AZURE.WARNING]Si vous redimensionnez une machine virtuelle et que cela la déplace vers un hôte différent, le disque temporaire devient le lecteur D.

##Modification de la lettre de lecteur

1. Connectez-vous à votre machine virtuelle. Pour plus d'informations, consultez [Connexion à une machine virtuelle exécutant Windows Server][Logon].

2. Déplacez pagefile.sys du lecteur D vers un autre lecteur.

3. Redémarrez la machine virtuelle.

4. Connectez-vous à nouveau et modifiez la lettre du lecteur D en E.

5. Depuis le [portail Azure](http://manage.windowsazure.com), associez un disque de données existant ou vide.

6.	Connectez-vous à nouveau à la machine virtuelle, initialisez le disque, puis attribuez D comme lettre de lecteur au disque que vous venez d'associer.

7.	Vérifiez que E fait référence au disque temporaire.

8.	Déplacez pagefile.sys de l'autre lecteur vers le lecteur E.

## Ressources supplémentaires
[Connexion à une machine virtuelle exécutant Windows Server][Logon]

[Détachement d’un disque de données d’une machine virtuelle Windows][Detach]

[À propos des comptes de stockage Azure][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md



[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=August15_HO6-->