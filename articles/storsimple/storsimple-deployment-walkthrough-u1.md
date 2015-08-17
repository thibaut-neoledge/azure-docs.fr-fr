<properties 
   pageTitle="Déploiement de votre appareil StorSimple local"
   description="Procédures et meilleures pratiques de déploiement du service et de l’appareil StorSimple Update 1."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/08/2015"
   ms.author="alkohli" />

# Déploiement de votre appareil StorSimple local

[AZURE.INCLUDE [storsimple-version-selector](../../includes/storsimple-version-selector.md)]

## Vue d’ensemble

Bienvenue dans cette série de didacticiels consacrée au déploiement d’appareils Microsoft Azure StorSimple.

Ces didacticiels de déploiement s’appliquent à StorSimple série 8000 Update 1.0.

Ils expliquent comment configurer votre appareil StorSimple et proposent une liste de contrôle à suivre avant l’installation, ainsi que la configuration requise et des étapes de configuration détaillées.

> [AZURE.NOTE]Les informations de déploiement StorSimple publiées sur le site web Microsoft Azure et dans la bibliothèque MSDN s’appliquent uniquement aux appareils StorSimple de la série 8000. Pour obtenir des informations complètes sur les appareils de la série 7000, consultez la page : [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Pour plus d’informations sur le déploiement de la série 7000, consultez le [Guide de démarrage rapide du système StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).

Les informations contenues dans ces didacticiels supposent que vous avez passé en revue les précautions de sécurité ainsi que de la liste de contrôle de configuration, et décompacté, monté en rack et câblé votre appareil StorSimple. Si vous devez effectuer ces tâches, consultez, le cas échéant, les guides [Précautions de sécurité](https://msdn.microsoft.com/library/azure/dn772366.aspx), [Liste de contrôle de configuration](https://msdn.microsoft.com/library/azure/dn757787.aspx) et [Installation matérielle de votre appareil](https://msdn.microsoft.com/library/azure/dn772375.aspx).

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous recommandons de consulter la liste de contrôle préalable à l’installation avant de commencer. Le processus de déploiement et de configuration peut prendre du temps.

## Liste de contrôle préalable à l’installation

La liste de contrôle préalable à l’installation suivante présente les informations dont vous avez besoin avant de configurer le logiciel de votre appareil StorSimple. L’étude préalable de ces informations simplifie le déploiement de l’appareil StorSimple dans votre environnement.

| | Conditions requises | Détails | Valeurs |
|---| --------------------- | ---------------------- | ------------- |
| 1 | Paramètres réseau <ol><li>Interfaces réseau, 4x1 GbE, 2x10 GbE</li><li>Adresse IP de contrôleur fixe</li><li>Masques de sous-réseau</li><li>Passerelle</li></ol> | Nombre total d’adresses IP requises : 8 <ol><li>Une par interface réseau activée, pour un nombre total de 6</li><li>Une par contrôleur, pour un nombre total de 2 (requises pour la connexion à Internet afin d’accéder aux mises à jour de service)</li><li>Une pour chaque adresse IP</li><li>Une par appareil</li></ol> | |
| 2 | Accès série | Configuration initiale de l’appareil | Oui/Non |
| 3 | Adresses IP du serveur DNS | Requises pour se connecter à Microsoft Azure : 2 requises pour la haute disponibilité | |
| 4 | Adresses IP du serveur NTP | Requises pour la synchronisation d’heure avec Azure : 1 obligatoire, 1 facultative | |
| 5 | Serveur proxy (facultatif) | Adresse IP/nom de domaine complet du serveur proxy, port à utiliser | |
| 6 | un compte Azure Storage ; | Permet d’accéder aux informations d’identification telles que le nom du compte et la clé d’accès, pour chaque compte de stockage | |
| 7 | Clé de chiffrement de stockage cloud (recommandé) | Par conteneur de volumes | |
| 8 | Nom qualifié de l’hôte | Par hôte | |

## Conditions préalables au déploiement

Les sections suivantes décrivent les conditions préalables à la configuration de votre service StorSimple Manager et de votre appareil StorSimple.

### Pour le service StorSimple Manager

Avant de commencer, assurez-vous que :

- Vous disposez d’un compte Microsoft doté d’informations d’identification d’accès.

- Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.

- Votre abonnement Microsoft Azure est activé pour le service StorSimple Manager. Votre abonnement doit être acheté en passant par la page [Licences Azure pour l’entreprise](http://azure.microsoft.com/pricing/enterprise-agreement/).

- Vous avez accès à un logiciel d’émulation de terminal tel que PuTTY.

### Pour l’appareil dans le centre de données

Avant de configurer l’appareil, assurez-vous que :

- Votre appareil est totalement déballé comme cela est indiqué dans les articles [Déballage de votre appareil 8100](https://msdn.microsoft.com/library/azure/dn772327.aspx) ou [Déballage de votre appareil 8600](https://msdn.microsoft.com/library/azure/dn772418.aspx).

- Votre appareil est monté en rack comme cela est indiqué dans les articles [Monter en rack votre appareil 8100](https://msdn.microsoft.com/library/azure/dn757749.aspx) ou [Monter en rack votre appareil 8600](https://msdn.microsoft.com/library/azure/dn757745.aspx).

- Votre appareil est correctement câblé à l’alimentation, au réseau et au port série comme cela est indiqué dans les articles [Câbler votre appareil 8100](https://msdn.microsoft.com/library/azure/dn757738.aspx) ou [Câbler votre appareil 8600](https://msdn.microsoft.com/library/azure/dn757762.aspx).

- Les ports du pare-feu de votre centre de données sont ouverts pour autoriser le trafic iSCSI et du cloud, comme décrit dans la section [Configuration réseau requise pour un appareil StorSimple](https://msdn.microsoft.com/library/dn772371.aspx).

## Étapes du déploiement

Suivez ces étapes requises pour configurer votre appareil StorSimple et le connecter à votre service StorSimple Manager :

- Étape 1 : Création d’un nouveau service 
- Étape 2 : Obtention de la clé d’inscription
- Étape 3 : Configuration et inscription de l’appareil via Windows PowerShell pour StorSimple 
- Étape 4 : Fin de l’installation minimale de l’appareil
- Étape 5 : Création d’un conteneur de volumes 
- Étape 6 : Création d’un volume
- Étape 7 : Montage, initialisation et formatage d’un volume 
- Étape 8 : Sauvegarde

Outre les étapes requises, il existe quelques étapes facultatives que vous devrez peut-être mener à bien au fur et à mesure du déploiement de votre solution. Ces étapes facultatives expliquent comment :

- configurer un nouveau compte de stockage pour le service ;
- utiliser PuTTY pour se connecter à la console série de l’appareil ;
- obtenir le nom qualifié d’un hôte Windows Server ;
- Création d’une sauvegarde manuelle
- configurer la solution MPIO.

Les instructions de déploiement étape par étape indiquent le moment où vous devez effectuer chacune de ces étapes facultatives.

## Étape 1 : Création d’un nouveau service

Un service StorSimple Manager peut gérer plusieurs appareils StorSimple. Procédez comme suit pour créer une instance du service StorSimple Manager.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT]Si vous n’avez pas activé la création automatique d’un compte de stockage avec votre service, vous devez créer au moins un compte de stockage après avoir créé un service. Ce compte de stockage est utilisé lorsque vous créez un conteneur de volumes.
>
> * Si vous n’avez pas créé de compte de stockage automatiquement, accédez à la page [Configuration d’un compte de stockage pour le service](#configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées. 
> * Si vous avez activé la création automatique d’un compte de stockage, passez à l’[étape 2 : Obtention de la clé d’inscription](#step-2:-get-the-service-registration-key).

## Étape 2 : Obtention de la clé d’inscription

Une fois le service StorSimple Manager opérationnel, vous devez obtenir la clé d’inscription. Cette clé est utilisée pour inscrire et connecter votre appareil StorSimple auprès du service.

Procédez comme suit dans le portail de gestion.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## Étape 3 : Configuration et inscription de l’appareil via Windows PowerShell pour StorSimple

Utilisez Windows PowerShell pour StorSimple pour terminer l’installation initiale de votre appareil StorSimple, comme expliqué dans la procédure suivante. Vous devez utiliser un logiciel d’émulation de terminal pour effectuer cette étape. Pour plus d’informations, consultez la rubrique [Utilisation de PuTTY pour se connecter à la console série de l’appareil](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## Étape 4 : Fin de l’installation minimale de l’appareil

Pour pouvoir mener à bien la configuration minimale de votre appareil StorSimple, vous devez :

- configurer le serveur DNS secondaire ;
- activer la norme iSCSI sur au moins une interface réseau ;
- affecter des adresses IP fixes aux deux contrôleurs.

Procédez comme suit dans le portail de gestion pour mener à bien la configuration minimale requise.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## Étape 5 : Création d’un conteneur de volumes

Un conteneur de volumes dispose de paramètres de compte de stockage, de bande passante et de chiffrement pour tous les volumes qu’il contient. Vous devez créer un conteneur de volumes avant de commencer la configuration des volumes sur votre appareil StorSimple.

Procédez comme suit dans le portail de gestion pour créer un conteneur de volumes.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Étape 6 : Création d’un volume

Après avoir créé un conteneur de volumes, vous pouvez configurer un volume de stockage sur l’appareil StorSimple pour vos serveurs. Procédez comme suit dans le portail de gestion pour créer un volume.

> [AZURE.IMPORTANT]Azure StorSimple peut créer uniquement des volumes alloués dynamiquement. Vous ne pouvez pas créer de volumes entièrement ou partiellement alloués sur un système Azure StorSimple.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## Étape 7 : Montage, initialisation et formatage d’un volume

Procédez comme suit sur votre hôte Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Étape 8 : Sauvegarde

Les sauvegardes fournissent une protection jusqu’à une date et une heure des volumes et optimisent la récupération tout en réduisant les délais de restauration. Vous pouvez effectuer deux types de sauvegarde sur votre appareil StorSimple : les instantanés locaux et les instantanés cloud. Chacun de ces types de sauvegarde peut être **Planifié** ou **Manuel**.

Procédez comme suit dans le portail de gestion pour créer une sauvegarde planifiée.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Vous pouvez à tout moment effectuer une sauvegarde manuelle. Pour connaître les procédures à suivre, consultez la rubrique [Création d’une sauvegarde manuelle](#create-a-manual-backup).

## Configuration d’un nouveau compte de stockage pour le service

Il s’agit d’une étape facultative que vous devez exécuter uniquement si vous n’avez pas activé la création automatique d’un compte de stockage avec votre service. Un compte de stockage Microsoft Azure est requis pour créer un conteneur de volumes StorSimple.

Si vous devez créer un compte de stockage Azure dans une autre région, consultez la page [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md) pour obtenir des instructions détaillées.

Procédez comme suit dans le portail de gestion, sur la page **Service StorSimple Manager**.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## Utilisation de PuTTY pour se connecter à la console série de l’appareil

Pour vous connecter à Windows PowerShell pour StorSimple, vous devez utiliser un logiciel d’émulation de terminal tel que PuTTY. Vous pouvez utiliser PuTTY lorsque vous accédez à l’appareil directement via la console série ou en ouvrant une session telnet à partir d’un ordinateur distant.

[AZURE.INCLUDE [Utilisation de PuTTY pour se connecter à la console série de l’appareil](../../includes/storsimple-use-putty.md)]

## Obtention du nom qualifié d’un hôte Windows Server

Procédez comme suit pour obtenir le nom qualifié iSCSI d’un hôte Windows exécutant Windows Server® 2012.

[AZURE.INCLUDE [Création d’une sauvegarde manuelle](../../includes/storsimple-get-iqn.md)]

## Création d’une sauvegarde manuelle

Procédez comme suit dans le portail de gestion pour créer une sauvegarde manuelle à la demande pour un seul volume sur votre appareil StorSimple.

[AZURE.INCLUDE [Création d’une sauvegarde manuelle](../../includes/storsimple-create-manual-backup.md)]

## Configuration de solution MPIO

La solution MPIO (Multipath I/O) est une fonctionnalité facultative qui n’est pas installée sur Windows Server par défaut. Elle doit être installée en tant que fonctionnalité via le Gestionnaire de serveur.

> [AZURE.NOTE]La solution MPIO n’est pas prise en charge sur un appareil virtuel StorSimple.

Pour obtenir les instructions d’installation de la solution MPIO, consultez la rubrique [Configuration de la solution MPIO pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md).

## Étapes suivantes

Configuration d’un [appareil virtuel](storsimple-virtual-device.md).

Utilisez le [service StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) pour gérer votre appareil StorSimple.
 

<!---HONumber=August15_HO6-->