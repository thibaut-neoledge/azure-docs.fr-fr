---
title: Déploiement de StorSimple Virtual Array 1 - Préparation du portail
description: Le premier didacticiel de déploiement de StorSimple Virtual Array implique la préparation du portail
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2016
ms.author: alkohli

---
# Déploiement de StorSimple Virtual Array - Préparation du portail
![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## Vue d'ensemble
Cet article s'applique à Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel StorSimple local ou appareil virtuel StorSimple) exécutant la version de mise à la disposition générale (mars 2016). Il s’agit du premier article de la série de didacticiels sur le déploiement nécessaires pour déployer complètement votre tableau virtuel en tant que serveur de fichiers ou serveur iSCSI. Cet article décrit la préparation nécessaire pour créer et configurer votre service StorSimple Manager avant la configuration d'un tableau virtuel. Cet article contient également des liens vers une liste de vérification de la configuration du déploiement ainsi que la configuration requise.

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous recommandons de consulter la liste de contrôle de configuration du déploiement avant de commencer. La préparation du portail prendra moins de 10 minutes.

Les informations publiées dans cet article s’appliquent au déploiement de StorSimple Virtual Array dans le portail Azure Classic, ainsi que dans Microsoft Azure Government Cloud.

### Prise en main
Le flux de travail du déploiement se compose de la préparation du portail, de la configuration d'un tableau virtuel dans votre environnement virtualisé et de la finalisation de la configuration. Pour commencer le déploiement de StorSimple Virtual Array en tant que serveur de fichiers ou serveur iSCSI, vous devrez consulter les ressources suivantes (articles et vidéos).

#### Articles sur le déploiement
Consultez les articles suivants dans l'ordre indiqué pour déployer votre StorSimple Virtual Array.

| **#** | **Dans cette étape** | **Vous effectuerez ceci...** | **Utilisez ces documents.** |
| --- | --- | --- | --- |
| 1\. |**Configuration du portail Azure Classic** |Créez et configurez votre service StorSimple Manager avant de configurer un appareil virtuel StorSimple. |[Préparation du portail](storsimple-ova-deploy1-portal-prep.md) |
| 2\. |**Configuration du tableau virtuel** |Pour Hyper-V, configurez et connectez-vous à un appareil virtuel StorSimple sur un système hôte exécutant Hyper-V sur Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. <br></br> <br></br> Pour VMware, configurez et connectez-vous à un appareil virtuel local StorSimple sur un système hôte exécutant VMware ESXi 5.5 et versions ultérieures.<br></br> |[Configuration d’un tableau virtuel dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Configuration d’un tableau virtuel dans VMware](storsimple-ova-deploy2-provision-vmware.md) |
| 3\. |**Configuration du tableau virtuel** |Pour votre serveur de fichiers, effectuez la configuration initiale, inscrivez votre serveur de fichiers StorSimple, puis finalisez la configuration de l’appareil. Vous pouvez ensuite configurer les partages SMB. <br></br> <br></br> Pour votre serveur iSCSI, effectuez la configuration initiale, inscrivez votre serveur iSCSI StorSimple, puis finalisez la configuration de l’appareil. Vous pouvez ensuite configurer les volumes iSCSI. |[Configuration d’un tableau virtuel comme serveur de fichiers](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configuration d’un virtuel tableau comme serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md) |

#### Vidéos de déploiement
| **Pour effectuer cette étape...** | **Regardez cette vidéo.** |
| --- | --- |
| Instructions pas à pas pour prendre en main StorSimple Virtual Array. |[Prise en main de StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/) |
| Instructions pas à pas pour configurer StorSimple Virtual Array dans Hyper-V. |[Création d’un StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
| Instructions pas à pas pour configurer et enregistrer StorSimple Virtual Array |[Configuration de StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/) |
| Instructions pas à pas pour créer des partages, sauvegarder des partages et restaurer des données sur un StorSimple Virtual Array configuré comme serveur de fichiers |[Utilisation de StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/) |
| Instructions pas à pas pour le basculement et la récupération d'urgence d'un StorSimple Virtual Array |[Récupération d’urgence d’un StorSimple Virtual Array](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/) |

Vous pouvez maintenant commencer à configurer le portail Azure Classic.

## Liste de contrôle de la configuration
La liste de contrôle de la configuration présente les informations dont vous avez besoin avant de configurer le logiciel de votre appareil StorSimple. L’étude préalable de ces informations simplifie le déploiement de l’appareil StorSimple dans votre environnement. Selon que votre appareil virtuel StorSimple sera déployé comme serveur de fichiers ou serveur iSCSI, effectuez une des listes de contrôle suivantes.

* Téléchargez la [liste de vérification de la configuration du serveur de fichiers StorSimple Virtual Array](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Téléchargez la [liste de vérification de la configuration du serveur iSCSI StorSimple Virtual Array](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## Configuration requise
Vous trouverez ici les conditions préalables à la configuration de votre service StorSimple Manager, de votre appareil virtuel StorSimple et du réseau de votre centre de données.

### Pour le service StorSimple Manager
Avant de commencer, assurez-vous que :

* Vous disposez d’un compte Microsoft doté d’informations d’identification d’accès.
* Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.
* Votre abonnement Microsoft Azure devrait être activé pour le service StorSimple Manager.

### Pour l’appareil virtuel StorSimple
Avant de déployer un appareil virtuel, assurez-vous que :

* Vous avez accès à un système hôte exécutant Hyper-V sur Windows Server 2008 R2 ou une version ultérieure ou VMware (ESXi 5.5 ou une version ultérieure) permettant de configurer un appareil.
* Le système hôte est en mesure de dédier les ressources suivantes pour configurer votre appareil virtuel :
  
  * Un minimum de 4 cœurs.
  * Au moins 8 Go de RAM.
  * Une interface réseau.
  * Un disque virtuel de 500 Go pour les données système.

### Pour le réseau du centre de données
Avant de commencer, assurez-vous que :

* Le réseau de votre centre de données est configuré conformément à la configuration réseau requise pour votre appareil StorSimple. Pour plus d'informations, consultez la [Configuration système requise pour StorSimple Virtual Array](storsimple-ova-system-requirements.md).
* Votre appareil virtuel StorSimple a une bande passante Internet dédiée de 5 Mbits/s (ou plus) disponible à tout moment. La bande passante ne doit pas être partagée avec d’autres applications.

## Préparation étape par étape
Utilisez la procédure pas à pas suivante afin de préparer votre portail pour le service StorSimple Manager.

## Étape 1 : Création d’un nouveau service
Une seule instance du service StorSimple Manager peut gérer plusieurs appareils StorSimple 1200. Procédez comme suit pour créer une instance du service StorSimple Manager. Si vous disposez d'un service StorSimple Manager existant pour gérer vos appareils 1200, ignorez cette étape et passez à l’[étape 2 : Obtention de la clé d’inscription](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [!IMPORTANT]
> Si vous n’avez pas activé la création automatique d’un compte de stockage avec votre service, vous devez créer au moins un compte de stockage après avoir créé un service.
> 
> * Si vous n’avez pas créé de compte de stockage automatiquement, accédez à la page [Configuration d’un compte de stockage pour le service](#optional-step-configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées.
> * Si vous avez activé la création automatique d’un compte de stockage, passez à l’[étape 2 : Obtention de la clé d’inscription](#step-2-get-the-service-registration-key).
> 
> 

## Étape 2 : Obtention de la clé d’inscription
Une fois le service StorSimple Manager opérationnel, vous devez obtenir la clé d’inscription. Cette clé est utilisée pour inscrire et connecter votre appareil StorSimple auprès du service.

Procédez comme suit dans le [Portail Azure Classic](https://manage.windowsazure.com/).

[!INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [!NOTE]
> La clé d’inscription au service sert à inscrire tous les appareils StorSimple Manager que vous devez inscrire auprès du service StorSimple Manager.
> 
> 

## Étape 3 : Téléchargement de l'image de l’appareil virtuel
Après avoir obtenu la clé d'inscription du service, vous devrez télécharger l'image de l’appareil virtuel appropriée pour configurer un appareil virtuel sur le système hôte. Les images d’appareil virtuel sont spécifiques au système d'exploitation et peuvent être téléchargées depuis la page Démarrage rapide du portail Azure Classic.

> [!IMPORTANT]
> Le logiciel exécuté sur StorSimple Virtual Array peut uniquement servir en association avec le service Storsimple Manager.
> 
> 

Procédez comme suit dans le [Portail Azure Classic](https://manage.windowsazure.com/).

#### Pour obtenir l'image de l’appareil virtuel
1. À la page **Service StorSimple Manager**, cliquez sur le service que vous avez créé. La page **Démarrage rapide** s’affiche. (Vous pouvez cliquer sur l’icône de démarrage rapide à tout moment ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) pour accéder à la page **Démarrage rapide**.)
2. Cliquez sur le lien correspondant à l’image que vous souhaitez télécharger à partir du Centre de téléchargement Microsoft. Les fichiers image ont une taille d’environ 4,8 Go.
   
   * VHDX pour Hyper-V sur Windows Server 2012 et versions ultérieures
   * VHD pour Hyper-V sur Windows Server 2008 R2 et versions ultérieures
   * VMDK pour VMWare ESXi 5.5 et versions ultérieures
3. Téléchargez et décompressez le fichier sur un lecteur local et notez l’emplacement du fichier décompressé.

![icône de vidéo](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **Vidéo disponible**

Regardez la vidéo afin d’obtenir des instructions pas à pas pour prendre en main StorSimple Virtual Array.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Get-Started-with-the-StorSimple-Virtual-Array/player]
> 
> 

## Étape facultative : configuration d’un nouveau compte de stockage pour le service
Il s’agit d’une étape facultative que vous devez exécuter uniquement si vous n’avez pas activé la création automatique d’un compte de stockage avec votre service.

Si vous devez créer un compte de stockage Azure dans une autre région, consultez la rubrique [Créez un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) pour obtenir des instructions détaillées.

Procédez comme suit dans le [portail Azure Classic](https://manage.windowsazure.com/) sur la page du service StorSimple Manager pour ajouter un compte de stockage Microsoft Azure existant.

#### Pour ajouter un compte de stockage
1. Sur la page d’accueil du service StorSimple Manager, sélectionnez votre service et double-cliquez dessus. La page **Démarrage rapide** s’affiche. Sélectionnez la page **Configurer**.
2. Cliquez sur **Ajouter/modifier un compte de stockage**. Dans la boîte de dialogue **Ajouter/modifier un compte de stockage**, procédez comme suit :
   
   1. Cliquez sur **Ajouter nouveau**.
   2. Entrez un nom pour votre compte de stockage.
   3. Indiquez la **Clé d’accès** principale pour votre compte de stockage Microsoft Azure.
   4. Sélectionnez **Activer le mode SSL** afin de créer un canal sécurisé pour la communication réseau entre votre appareil et le cloud. Décochez la case **Activer le mode SSL** uniquement si vous utilisez l’appareil dans un cloud privé.
   5. Cliquez sur l’icône en forme de coche ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Un message s’affiche pour vous informer une fois le compte de stockage correctement créé.
      
      ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)
3. Le compte de stockage qui vient d’être créé est affiché dans la page **Configurer**, sous **Comptes de stockage**. Cliquez sur **Enregistrer** pour enregistrer le nouveau compte de stockage. Cliquez sur **OK** lorsque vous êtes invité à confirmer l’opération.

## Étape suivante
L'étape suivante consiste à configurer une machine virtuelle pour votre appareil virtuel StorSimple. Selon votre système d'exploitation hôte, consultez les instructions détaillées dans :

* [Configurer StorSimple Virtual Array dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)
* [Configurer StorSimple Virtual Array dans VMware](storsimple-ova-deploy2-provision-vmware.md)

<!---HONumber=AcomDC_0601_2016-->