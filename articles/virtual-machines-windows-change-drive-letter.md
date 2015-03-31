<properties 
	pageTitle="Modification de la lettre de lecteur du disque temporaire Windows" 
	description="Explique comment remapper le disque temporaire sur une machine virtuelle Windows dans Azure" 
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
	ms.date="01/15/2015" 
	ms.author="kathydav"/>

# Modification de la lettre de lecteur du disque temporaire Windows

Si vous avez besoin d'utiliser le lecteur D pour stocker des données, suivez ces instructions pour utiliser un autre lecteur de disque temporaire. N'utilisez jamais le lecteur temporaire pour stocker des données à conserver.

Avant de commencer, vous aurez besoin d'un disque de données attaché à la machine virtuelle afin d'y enregistrer le fichier d'échange Windows (pagefile.sys) au cours de cette procédure. Consultez [Association d'un disque de données à une machine virtuelle Windows][Attachement d'un disque de données à une machine virtuelle Windows] si vous n'en avez pas. Pour savoir quels disques sont attachés, consultez la rubrique " Gérer vos disques " dans [À propos des disques de machines virtuelles dans Azure].

Si vous souhaitez utiliser un disque de données existant sur le lecteur D, assurez-vous que vous avez également téléchargé le disque dur virtuel vers le compte de stockage. Pour plus d'instructions, consultez les étapes 3 et 4 de la rubrique [Création et téléchargement d'un disque dur virtuel Windows Server vers Azure][Création et téléchargement d'un disque dur virtuel Windows Server dans Azure].

> [AZURE.AVERTISSEMENT] Si vous redimensionnez une machine virtuelle et que cela la déplace vers un hôte différent, le lecteur temporaire devient le lecteur D.

## Modification de la lettre de lecteur

1. Connectez-vous à la machine virtuelle. 

2. Déplacez pagefile.sys du lecteur D vers un autre lecteur.

3. Redémarrez la machine virtuelle.

4. 	Connectez-vous à nouveau et modifiez la lettre du lecteur D en E.

5.	Depuis le [portail de gestion Azure](http://manage.windowsazure.com), associez un disque de données existant ou vide.

6.	Connectez-vous à nouveau à la machine virtuelle, initialisez le disque, puis attribuez D comme lettre de lecteur au disque que vous venez d'associer.

7.	Vérifiez que E fait référence au disque de stockage temporaire.

8.	Déplacez pagefile.sys de l'autre lecteur vers le lecteur E.

## Ressources supplémentaires
[Connexion à une machine virtuelle exécutant Windows Server]

[Détachement d'un disque de données d'une machine virtuelle]

[À propos des comptes de stockage Azure][À propos des comptes Azure Storage]

<!--Link references-->
[Attachement d'un disque de données à une machine virtuelle Windows]: ../storage-windows-attach-disk
[À propos des disques de machines virtuelles dans Azure]: ../http://msdn.microsoft.com/library/azure/dn790303.aspx
[Création et téléchargement d'un disque dur virtuel Windows Server dans Azure]: ../virtual-machines-create-upload-vhd-windows-server/
[Connexion à une machine virtuelle exécutant Windows Server]: ../virtual-machines-log-on-windows-server/
[Détachement d'un disque de données d'une machine virtuelle]: ../storage-windows-detach-disk/
[À propos des comptes Azure Storage]: ../storage-whatis-account/



<!--HONumber=47-->
