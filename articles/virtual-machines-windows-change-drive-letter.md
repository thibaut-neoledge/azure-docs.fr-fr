<properties title="Modification de la lettre de lecteur du disque temporaire Windows" pageTitle="Modification de la lettre de lecteur du disque temporaire Windows" description="Explique comment remapper le disque temporaire sur une machine virtuelle&nbsp;Windows dans&nbsp;Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav"  manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="07/09/2014" ms.author="kathydav" />

# Modification de la lettre de lecteur du disque temporaire Windows

Vous pouvez changer la lettre de lecteur du disque temporaire si vous avez besoin d'utiliser le lecteur D pour autre chose. Vous devrez sans doute le faire pour prendre en charge une application ou un service utilisant le lecteur D comme emplacement de stockage permanent.

Avant de commencer, vérifiez que vous avez les éléments suivants :

-   Un disque de données associé que vous pouvez utiliser pour stocker le fichier d'échange de Windows (pagefile.sys) lors de cette procédure. Pour plus d’informations, consultez la rubrique [Association d’un disque de données avec une machine virtuelle][Association d’un disque de données avec une machine virtuelle].
-   Un disque dur virtuel téléchargé dans le compte de stockage, si vous souhaitez utiliser un disque dur virtuel d'un disque de données existant sur le lecteur D. Pour plus d'instructions, consultez les étapes 3 et 4 de la rubrique [Création et téléchargement d'un disque dur virtuel Windows Server vers Azure][Création et téléchargement d'un disque dur virtuel Windows Server vers Azure].

## Modification de la lettre de lecteur

1.  Connectez-vous à la machine virtuelle.

2.  Déplacez pagefile.sys du lecteur D vers un autre lecteur.

3.  Redémarrez la machine virtuelle.

4.  Connectez-vous à nouveau et modifiez la lettre du lecteur D en E.

5.  Depuis le [portail de gestion Azure][portail de gestion Azure], associez un disque de données existant ou vide.

6.  Connectez-vous à nouveau à la machine virtuelle, initialisez le disque, puis attribuez D comme lettre de lecteur au disque que vous venez d'associer.

7.  Vérifiez que E fait référence au disque de stockage temporaire.

8.  Déplacez pagefile.sys de l'autre lecteur vers le lecteur E.

## Ressources supplémentaires

[Connexion à une machine virtuelle exécutant Windows Server][Connexion à une machine virtuelle exécutant Windows Server]

[Détachement d’un disque de données d’une machine virtuelle][Détachement d’un disque de données d’une machine virtuelle]

[Présentation des comptes de stockage][Présentation des comptes de stockage]

<!--Link references-->

  [Association d’un disque de données avec une machine virtuelle]: ../storage-windows-attach-disk
  [Création et téléchargement d'un disque dur virtuel Windows Server vers Azure]: ../virtual-machines-create-upload-vhd-windows-server/
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Connexion à une machine virtuelle exécutant Windows Server]: ../virtual-machines-log-on-windows-server/
  [Détachement d’un disque de données d’une machine virtuelle]: ../storage-windows-detach-disk/
  [Présentation des comptes de stockage]: ../storage-whatis-account/
