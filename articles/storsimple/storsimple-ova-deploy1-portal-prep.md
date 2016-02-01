<properties
   pageTitle="Déploiement de StorSimple Virtual Array 1 - Préparation du portail"
   description="Le premier didacticiel de déploiement de StorSimple Virtual Array implique la préparation du portail"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="alkohli"/>

# Déploiement de StorSimple Virtual Array - Préparation du portail (version préliminaire)

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## Introduction 

Cet article s'applique à Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel StorSimple local ou appareil virtuel StorSimple) exécutant la version 1.1.1.0 (version préliminaire publique) uniquement. Il s’agit du premier article de la série de didacticiels sur le déploiement nécessaires pour déployer complètement votre tableau virtuel en tant que serveur de fichiers ou serveur iSCSI. Cet article décrit la préparation nécessaire pour créer et configurer votre service StorSimple Manager avant la configuration d'un tableau virtuel. Cet article contient également des liens vers une liste de vérification de la configuration du déploiement ainsi que la configuration requise.

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous recommandons de consulter la liste de contrôle de configuration du déploiement avant de commencer. La préparation du portail prendra moins de 10 minutes.

Les informations de déploiement StorSimple publiées dans cet article s'appliquent uniquement aux tableaux virtuels StorSimple.

> [AZURE.IMPORTANT]
> 
> Cette version préliminaire publique est destinée uniquement à des fins d’évaluation et de planification du déploiement. L'installation de cette version préliminaire dans un environnement de production n'est pas prise en charge.

### Prise en main

Le flux de travail du déploiement se compose de la préparation du portail, de la configuration d'un tableau virtuel dans votre environnement virtualisé et de la finalisation de la configuration. Pour commencer le déploiement de StorSimple Virtual Array en tant que serveur de fichiers ou serveur iSCSI, vous devrez consulter les ressources suivantes (articles et vidéos).

#### Articles sur le déploiement

Consultez les articles suivants dans l'ordre indiqué pour déployer votre StorSimple Virtual Array.

| **#** | **Dans cette étape** | **Vous effectuerez ceci...** | **Utilisez ces documents...**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1\. | **Configuration du portail Azure Classic** | Créez et configurez votre service StorSimple Manager avant de configurer un appareil virtuel StorSimple. |[Préparation du portail](storsimple-ova-deploy1-portal-prep.md)| 
|2\. | **Configuration du tableau virtuel** | Pour Hyper-V, configurez et connectez-vous à un appareil virtuel StorSimple sur un système hôte exécutant Hyper-V 2008 R2, Hyper-V 2012 ou Hyper-V 2012 R2. <br></br> <br></br> Pour VMware, configurez et connectez-vous à un appareil virtuel local StorSimple sur un système hôte exécutant VMware ESXi 5.5 et versions ultérieures.| [Configuration d’un tableau virtuel dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Configuration d’un tableau virtuel dans VMware](storsimple-ova-deploy2-provision-vmware.md)|
|3\. | **Configuration du tableau virtuel** | Pour votre serveur de fichiers, effectuez la configuration initiale, inscrivez votre serveur de fichiers StorSimple, puis finalisez la configuration de l’appareil. Vous pouvez ensuite configurer les partages SMB. <br></br> <br></br> Pour votre serveur iSCSI, effectuez la configuration initiale, inscrivez votre serveur iSCSI StorSimple, puis finalisez la configuration de l’appareil. Vous pouvez ensuite configurer les volumes iSCSI.| [Configuration d’un tableau comme serveur de fichiers](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configuration d’un tableau comme serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md)|

#### Vidéos de déploiement

| **Pour effectuer cette étape...** | **Regardez cette vidéo**|
|----------------|-------------|
| Instructions pas à pas pour prendre en main StorSimple Virtual Array. | [Prise en main de StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array)|
| Instructions pas à pas pour configurer StorSimple Virtual Array dans Hyper-V.|[Création d’un StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array) |
|Instructions pas à pas pour configurer et enregistrer StorSimple Virtual Array|[Configuration de StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array)|
|Instructions pas à pas pour créer des partages, sauvegarder des partages et restaurer des données sur un StorSimple Virtual Array configuré comme serveur de fichiers|[Utilisation de StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array)|
|Instructions pas à pas pour le basculement et la récupération d'urgence d'un StorSimple Virtual Array|[Récupération d’urgence d’un StorSimple Virtual Array](http://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery)

Vous pouvez maintenant commencer à configurer le portail Azure Classic.

## Liste de contrôle de la configuration

La liste de contrôle de la configuration présente les informations dont vous avez besoin avant de configurer le logiciel de votre appareil StorSimple. L’étude préalable de ces informations simplifie le déploiement de l’appareil StorSimple dans votre environnement. Selon que votre appareil virtuel StorSimple sera déployé comme serveur de fichiers ou serveur iSCSI, effectuez une des listes de contrôle suivantes.

-   Téléchargez la [liste de vérification de la configuration du serveur de fichiers StorSimple Virtual Array](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Téléchargez la [liste de vérification de la configuration du serveur iSCSI StorSimple Virtual Array](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## Configuration requise

Vous trouverez ici les conditions préalables à la configuration de votre service StorSimple Manager, de votre appareil virtuel StorSimple et du réseau de votre centre de données.

### Pour le service StorSimple Manager

Avant de commencer, assurez-vous que :

-   Vous disposez d’un compte Microsoft doté d’informations d’identification d’accès.

-   Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.

-   Votre abonnement Microsoft Azure devrait être activé pour le service StorSimple Manager.

### Pour l’appareil virtuel StorSimple 

Avant de déployer un appareil virtuel, assurez-vous que :

-   Vous avez accès à un système hôte exécutant Hyper-V (2008 R2 ou version ultérieure) ou VMware (ESXi 5.5 ou version ultérieure) permettant de configurer un appareil.

-   Le système hôte est en mesure de dédier les ressources suivantes pour configurer votre appareil virtuel :
	
	-   Un minimum de 4 cœurs.
	
	-   Au moins 8 Go de RAM.
	
	-   Une interface réseau.
	
	-   Un disque virtuel de 500 Go pour les données système.

### Pour le réseau du centre de données 

Avant de commencer, assurez-vous que :

-   Le réseau de votre centre de données est configuré conformément à la configuration réseau requise pour votre appareil StorSimple. Pour plus d'informations, consultez la [Configuration système requise pour StorSimple Virtual Array](storsimple-ova-system-requirements.md).

-   Votre appareil virtuel StorSimple a une bande passante Internet dédiée de 5 Mbits/s (ou plus) disponible à tout moment. La bande passante ne doit pas être partagée avec d’autres applications.

## Préparation étape par étape

Utilisez la procédure pas à pas suivante afin de préparer votre portail pour le service StorSimple Manager.

## Étape 1 : Création d’un nouveau service

Une seule instance du service StorSimple Manager peut gérer plusieurs appareils StorSimple 1200. Procédez comme suit pour créer une instance du service StorSimple Manager. Si vous disposez d'un service StorSimple Manager existant pour gérer vos appareils 1200, ignorez cette étape et passez à l’[étape 2 : Obtention de la clé d’inscription](#step-2-get-the-service-registration-key).

#### Création d’un service

1.  À l’aide des informations d’identification de votre compte Microsoft, connectez-vous au portail Azure Classic en utilisant l’URL qui suit : <https://manage.windowsazure.com/>

2.  Dans le portail, cliquez sur **Nouveau > Services de données > StorSimple Manager > Création rapide**.

3.  Dans le formulaire qui s’affiche, procédez comme suit :

	1.  Indiquez un **nom** unique pour votre service. Il s’agit d’un nom convivial qui peut être utilisé pour identifier le service. Le nom peut comporter entre 2 et 50 caractères qui peuvent être des lettres, des chiffres et des traits d’union. Il doit commencer et se terminer par une lettre ou un chiffre.

	2.  Pour permettre à un service de gérer un appareil virtuel StorSimple, dans le menu déroulant des **Types d’appareils gérés**, choisissez **Série d’appareils virtuels**.

	3.  Indiquez un **emplacement** pour votre service. L’emplacement fait référence à la région géographique dans laquelle vous souhaitez déployer votre appareil.

	 -   Si vous avez d’autres charges de travail dans Azure que vous souhaitez déployer avec votre appareil StorSimple, nous vous invitons à utiliser ce centre de données.

   	 -   Le gestionnaire StorSimple et le stockage Azure peuvent se trouver en deux emplacements différents. Dans ce cas, vous devez créer séparément le gestionnaire StorSimple et le compte de stockage Azure. Pour créer un compte de stockage Azure, accédez au service Azure Storage dans le portail et suivez les étapes indiquées dans [Créer un compte Azure Storage](storage-create-storage-account.md#create-a-storage-account). Une fois ce compte créé, ajoutez-le au service StorSimple Manager en suivant les étapes indiquées dans [Configurer un nouveau compte de stockage pour le service](#appendix-a-configure-a-new-storage-account-for-the-service).
   	 
   	 -   Notez que dans la version préliminaire, vous pouvez créer un service StorSimple Manager uniquement dans l'Ouest des États-Unis et dans l’Est du Japon.
	
	1.  Choisissez un **abonnement** dans la liste déroulante. L’abonnement est lié à votre compte de facturation. Ce champ n’est pas présent si vous n’avez qu’un seul abonnement.

	1.  Sélectionnez **Créer un compte de stockage Azure** pour créer automatiquement un compte de stockage avec le service. Ce compte de stockage possédera un nom spécial, tel que « storsimplebwv8c6dcnf ». Si vous avez besoin de vos données dans un autre emplacement, désactivez cette case à cocher.

	1.  Cliquez sur **Créer StorSimple Manager** pour créer le service.

		![](./media/storsimple-ova-deploy1-portal-prep/image1.png)

	Vous allez être redirigé vers la page d’accueil **Service**. La création de service dure quelques minutes. Un message vous informe que le service a été créé.

	![](./media/storsimple-ova-deploy1-portal-prep/image2.png)

	Le statut du service sera alors **Actif**.

	![](./media/storsimple-ova-deploy1-portal-prep/image3.png)

> [AZURE.IMPORTANT]
> 
> Si vous n’avez pas activé la création automatique d’un compte de stockage avec votre service, vous devez créer au moins un compte de stockage après avoir créé un service.
> 

> - Si vous n’avez pas créé de compte de stockage automatiquement, accédez à la page [Configuration d’un compte de stockage pour le service](#appendix-a-configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées.
> 

> - Si vous avez activé la création automatique d’un compte de stockage, passez à l’[étape 2 : Obtention de la clé d’inscription](#step-2-get-the-service-registration-key).


## Étape 2 : Obtention de la clé d’inscription


Une fois le service StorSimple Manager opérationnel, vous devez obtenir la clé d’inscription. Cette clé est utilisée pour inscrire et connecter votre appareil StorSimple auprès du service.

Procédez comme suit dans le [Portail Azure Classic](https://manage.windowsazure.com/).

#### Pour obtenir la clé d’inscription au service StorSimple

1.  En bas de la page **Service StorSimple Manager**, cliquez sur **Clé d’inscription**.

2.  Vous devez patienter quelques minutes pendant la récupération de la clé. La boîte de dialogue **Clé d’inscription au service** s’affiche.


	1.  Repérez la **clé d’inscription au service**.

	2.  Cliquez sur l’icône de copie ![](./media/storsimple-ova-deploy1-portal-prep/image6.png) pour copier la clé et l’enregistrer pour une utilisation ultérieure.

> [AZURE.NOTE]
> 
> La clé d’inscription au service sert à inscrire tous les appareils StorSimple Manager que vous devez inscrire auprès du service StorSimple Manager.

## Étape 3 : Téléchargement de l'image de l’appareil virtuel

Après avoir obtenu la clé d'inscription du service, vous devrez télécharger l'image de l’appareil virtuel appropriée pour configurer un appareil virtuel sur le système hôte. Les images d’appareil virtuel sont spécifiques au système d'exploitation et peuvent être téléchargées depuis la page Démarrage rapide du portail Azure Classic.

Procédez comme suit dans le [Portail Azure Classic](https://manage.windowsazure.com/).

#### Pour obtenir l'image de l’appareil virtuel

1.  À la page **Service StorSimple Manager**, cliquez sur le service que vous avez créé. La page **Démarrage rapide** s’affiche. (Vous pouvez cliquer sur l’icône de démarrage rapide à tout moment ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) pour accéder à la page **Démarrage rapide**.)


1.  Téléchargez le disque dur virtuel approprié sur un partage réseau de votre centre de données. Des disques durs virtuels distincts sont disponibles pour :

	-   Hyper-V 2008 R2 et versions ultérieures

	-   VMWare ESXi 5.5 et versions ultérieures

1.  Cliquez sur l'image correspondant au système d'exploitation que vous allez utiliser pour configurer l’appareil virtuel. Vous serez dirigé vers le Centre de téléchargement Microsoft.

1.  Si vous utilisez Hyper-V, téléchargez le disque dur virtuel pour Hyper-V 2008 R2 et versions ultérieures. Si vous utilisez VMware, téléchargez le VMDK. Le disque dur virtuel est un fichier compressé de 4,77 Go, et le VMDK est un fichier de 4,75 Go. La durée de téléchargement du fichier dépend de votre connexion Internet.

2.  Décompressez le fichier et notez l'emplacement décompressé sur votre lecteur local.

![icône de vidéo](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **Vidéo disponible**

Regardez la vidéo afin d’obtenir des instructions pas à pas pour prendre en main StorSimple Virtual Array.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]

## Étape suivante

L'étape suivante consiste à configurer une machine virtuelle pour votre appareil virtuel StorSimple. Selon votre système d'exploitation hôte, consultez les instructions détaillées dans :

-   [Configuration de StorSimple Virtual Array dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Configuration de StorSimple Virtual Array dans VMware](storsimple-ova-deploy2-provision-vmware.md)


## Annexe A : Configuration d’un nouveau compte de stockage pour le service

Il s’agit d’une étape facultative que vous devez exécuter uniquement si vous n’avez pas activé la création automatique d’un compte de stockage avec votre service.

Si vous devez créer un compte de stockage Azure dans une autre région, consultez la page [Création d’un compte de stockage](storage-create-storage-account.md#create-a-storage-account) pour obtenir des instructions détaillées.

Procédez comme suit dans le [portail Azure Classic](https://manage.windowsazure.com/) sur la page du service StorSimple Manager pour ajouter un compte de stockage Microsoft Azure existant.

#### Pour ajouter un compte de stockage

1.  Sur la page d’accueil du service StorSimple Manager, sélectionnez votre service et double-cliquez dessus. La page **Démarrage rapide** s’affiche. Sélectionnez la page **Configurer**.

2.  Cliquez sur **Ajouter/modifier un compte de stockage**. Dans la boîte de dialogue **Ajouter/modifier un compte de stockage**, procédez comme suit :

	1.  Cliquez sur **Ajouter nouveau**.

	1.  Entrez un nom pour votre compte de stockage.

	1.  Indiquez la **Clé d’accès** principale pour votre compte de stockage Microsoft Azure.

	1.  Sélectionnez **Activer le mode SSL** afin de créer un canal sécurisé pour la communication réseau entre votre appareil et le cloud. Décochez la case **Activer le mode SSL** uniquement si vous utilisez l’appareil dans un cloud privé.

	1.  Cliquez sur l’icône en forme de coche ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Un message s’affiche pour vous informer une fois le compte de stockage correctement créé.

		![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  Le compte de stockage qui vient d’être créé est affiché dans la page **Configurer**, sous **Comptes de stockage**. Cliquez sur **Enregistrer** pour enregistrer le nouveau compte de stockage. Cliquez sur **OK** lorsque vous êtes invité à confirmer l’opération.

<!---HONumber=AcomDC_0121_2016-->