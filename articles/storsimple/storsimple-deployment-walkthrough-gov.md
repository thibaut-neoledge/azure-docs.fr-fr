<properties 
   pageTitle="Déployer un appareil StorSimple dans Government Portal | Microsoft Azure"
   description="Décrit les procédures et meilleures pratiques de déploiement du service et de l’appareil StorSimple Update 1 dans Microsoft Azure Government Portal."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/09/2015"
   ms.author="v-sharos" />

# Déploiement de votre appareil StorSimple local sur Microsoft Azure Government Portal

[AZURE.INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## Vue d’ensemble

Bienvenue dans cette série de didacticiels consacrée au déploiement d’appareils Microsoft Azure StorSimple. Ces didacticiels de déploiement s’appliquent à la gamme StorSimple série 8000 avec logiciel Update 1 dans Microsoft Azure Government Portal. Ils expliquent comment configurer votre appareil StorSimple et proposent une liste de contrôle de la configuration, ainsi que la configuration requise et des étapes de configuration détaillées.

Les informations contenues dans ces didacticiels supposent que vous avez passé en revue les précautions de sécurité, et décompacté, monté en rack et câblé votre appareil StorSimple. Si vous devez encore effectuer ces tâches, commencez par lire les [précautions de sécurité](storsimple-safety.md). Selon le modèle de votre appareil, vous pouvez ensuite décompacter, monter en rack et câbler avec les instructions suivantes :

- [Décompacter, monter en rack et câbler votre appareil 8100](storsimple-8100-hardware-installation.md)
- [Décompacter, monter en rack et câbler votre appareil 8600](storsimple-8600-hardware-installation.md)

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous recommandons de consulter la liste de contrôle de configuration avant de commencer. Le processus de déploiement et de configuration peut prendre du temps.

> [AZURE.NOTE]Les informations de déploiement StorSimple publiées sur le site web Microsoft Azure s’appliquent aux appareils StorSimple de la série 8000 uniquement. Pour obtenir des informations complètes sur les appareils de la série 7000, consultez la page : [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Pour plus d’informations sur le déploiement de la série 7000, consultez le [Guide de démarrage rapide du système StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).

## Étapes du déploiement

Suivez ces étapes requises pour configurer votre appareil StorSimple et le connecter à votre service StorSimple Manager : Outre les étapes requises, il existe des étapes et des procédures facultatives pendant le déploiement. Les instructions de déploiement étape par étape indiquent le moment où vous devez effectuer chacune de ces étapes facultatives.


| Étape | Description |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **CONFIGURATION REQUISE** | Ces étapes doivent être effectuées en préparation du déploiement à venir. |
| Liste de contrôle de la configuration du déploiement. | Utilisez cette liste de contrôle pour rassembler et enregistrer des informations avant et pendant le déploiement. |
| Conditions préalables au déploiement. | Celles-ci valident que l'environnement est prêt pour le déploiement. |
| | |
| **DÉPLOIEMENT ÉTAPE PAR ÉTAPE** | Ces étapes sont nécessaires pour déployer votre appareil StorSimple en production. |
| Étape 1 : Création d'un service. | Configurez le stockage et la gestion de cloud pour votre appareil StorSimple. Ignorez cette étape si vous avez un service existant pour d'autres appareils StorSimple. |
| Étape 2 : Obtention de la clé d'inscription. | Utilisez cette clé pour inscrire et connecter votre appareil StorSimple auprès du service de gestion. |
| Étape 3 : Configuration et inscription de l'appareil via Windows PowerShell pour StorSimple. | Connectez l'appareil à votre réseau et enregistrez-le avec Azure pour terminer l'installation à l'aide du service de gestion. |
| Étape 4 : Fin de l'installation minimale de l'appareil</br>Facultatif : mise à jour de votre appareil StorSimple. | Utilisez le service de gestion pour terminer la configuration de l'appareil et lui permettre d'assurer le stockage. |
| Étape 5 : Création d'un conteneur de volumes. | Créez un conteneur pour mettre en service les volumes. Un conteneur de volumes dispose de paramètres de compte de stockage, de bande passante et de chiffrement pour tous les volumes qu’il contient. |
| Étape 6 : Création d'un volume. | Mettez en service le(s) volume(s) de stockage sur l'appareil StorSimple pour vos serveurs. |
| Étape 7 : Montage, initialisation et formatage d'un volume.</br>Facultatif : Configuration de solution MPIO | Connectez vos serveurs au stockage iSCSI fourni par l'appareil. Vous avez la possibilité de configurer la solution MPIO pour vous assurer que vos serveurs peuvent tolérer une panne de liaison, de réseau et d’interface. |
| Étape 8 : Sauvegarde. | Configurez votre stratégie de sauvegarde pour protéger vos données. |
| | |
| **AUTRES PROCÉDURES** | Vous devrez peut-être faire référence à ces procédures lors du déploiement de votre solution. |
| Configuration d'un nouveau compte de stockage pour le service. | |
| Utilisation de PuTTY pour se connecter à la console série de l'appareil. | |
| Recherche et application des mises à jour. | |
| Obtention du nom qualifié d'un hôte Windows Server. | |
| Création d'une sauvegarde manuelle. | 
| Configuration de MPIO. |


## Liste de contrôle de la configuration du déploiement

La liste de contrôle de configuration du déploiement suivante présente les informations dont vous avez besoin avant et pendant la configuration du logiciel de votre appareil StorSimple. L'étude préalable de ces informations simplifie le déploiement de l'appareil StorSimple dans votre environnement. Utilisez cette liste de contrôle pour noter également les détails de configuration lors du déploiement de votre appareil.

| Étape | Paramètre | Détails | Valeurs |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Câblage de l'appareil** | Accès série | Configuration initiale de l’appareil | Oui/Non |
| | | | |
| **Configuration et inscription de l'appareil** | Paramètres réseau Data 0 | Adresse IP Data 0 :</br>Masque de sous-réseau :</br>Passerelle :</br>Serveur DNS principal :</br>Serveur NTP principal :</br>Adresse IP/nom de domaine complet (facultatif) du serveur proxy Web :</br>Port proxy Web :| |
| | Mot de passe d'administrateur de l'appareil | Le mot de passe doit contenir entre 8 et 15 caractères en minuscules, en majuscules, numériques et spéciaux. | |
| | Mot de passe de gestionnaire d’instantanés StorSimple | Le mot de passe doit contenir 14 ou 15 caractères en minuscules, en majuscules, numériques et spéciaux.| |
| | Clé d'inscription du service | Cette clé est générée à partir du portail Azure. | |
| | Clé de chiffrement des données du service | Cette clé est créée lorsque l'appareil est inscrit auprès du service de gestion par le biais de Windows PowerShell pour StorSimple. Copiez-la et enregistrez-la en lieu sûr.| |
| | | | |
| **Mener à bien l'installation minimale de l’appareil** | Nom convivial pour votre appareil | Il s'agit d'un nom descriptif pour l'appareil. | |
| | Fuseau horaire | Votre appareil utilise ce fuseau horaire pour toutes les opérations planifiées. | |
| | Serveur DNS secondaire | Il s'agit d'une configuration requise. | |
| | Interface réseau : Adresses IP fixes de contrôleur Data 0 | Ces adresses IP doivent être routables vers Internet.</br>Adresse IP fixe de contrôleur 0 :</br>Adresse IP fixe de contrôleur 1 :|
| | | | |
| **Paramètres d'interface réseau supplémentaires** | Interface réseau : Data 1</br>Si iSCSI est activé, ne configurez pas la passerelle. | Objectif : cloud/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle :|
| | Interface réseau : Data 2</br>Si iSCSI est activé, ne configurez pas la passerelle. | Objectif : cloud/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle :|
| | Interface réseau : Data 3</br>Si iSCSI est activé, ne configurez pas la passerelle. | Objectif : cloud/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle :|
| | Interface réseau : Data 4</br>Si iSCSI est activé, ne configurez pas la passerelle. | Objectif : cloud/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle :|
| | Interface réseau : Data 5</br>Si iSCSI est activé, ne configurez pas la passerelle. | Objectif : cloud/iSCSI/non utilisé</br>Adresse IP :</br>Masque de sous-réseau :</br>Passerelle :|
| | | | |
| **Création d’un conteneur de volumes** | Nom du conteneur de volumes : | nom du conteneur | |
| | Compte Azure Storage : | nom et clé d'accès du compte de stockage à associer à ce conteneur de volume | |
| | Clé de chiffrement de stockage cloud : | clé de chiffrement pour le stockage dans chaque conteneur | |
| | | | |
| **Création d’un volume** | Détails de chaque volume | Nom du volume : | |
| | | Taille : | |
| | | Type d'utilisation : | |
| | | Nom ACR : | |
| | | Stratégie de sauvegarde par défaut : | |
| | | | |
| **Monter, initialiser et formater un volume** | Détails pour chaque serveur hôte se connectant au stockage | Nom du serveur Windows : | |
| | | IQN du serveur Windows : | |
| | | Nom de volume du serveur Windows : | |
| | | Point de montage/lettre de lecteur NTFS : | |


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

- Votre appareil est correctement décompacté, monté en rack et câblé à l’alimentation, au réseau et au port série comme cela est indiqué dans les articles suivants :

	-  [Décompacter, monter en rack et câbler votre appareil 8100](storsimple-8100-hardware-installation.md)
	-  [Décompacter, monter en rack et câbler votre appareil 8600](storsimple-8600-hardware-installation.md)


### Pour le réseau dans le centre de données

Avant de commencer, assurez-vous que :

- Les ports du pare-feu de votre centre de données sont ouverts pour autoriser le trafic iSCSI et du cloud, comme décrit dans la section [Configuration réseau requise pour un appareil StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

## Déploiement étape par étape

Utilisez la procédure détaillée ci-dessous pour déployer votre appareil StorSimple dans le centre de données.

## Étape 1 : Création d’un nouveau service

Un service StorSimple Manager peut gérer plusieurs appareils StorSimple. Procédez comme suit pour créer une instance du service StorSimple Manager.

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT]Si vous n’avez pas activé la création automatique d’un compte de stockage avec votre service, vous devez créer au moins un compte de stockage après avoir créé un service. Ce compte de stockage est utilisé lorsque vous créez un conteneur de volumes.
>
> * Si vous n’avez pas créé de compte de stockage automatiquement, accédez à la page [Configuration d’un compte de stockage pour le service](#configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées. 
> * Si vous avez activé la création automatique d’un compte de stockage, passez à l’[étape 2 : Obtention de la clé d’inscription](#step-2-get-the-service-registration-key).

## Étape 2 : Obtention de la clé d’inscription

Une fois le service StorSimple Manager opérationnel, vous devez obtenir la clé d’inscription. Cette clé est utilisée pour inscrire et connecter votre appareil StorSimple auprès du service.

Procédez comme suit dans Microsoft Azure Government Portal.

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## Étape 3 : Configuration et inscription de l’appareil via Windows PowerShell pour StorSimple

Utilisez Windows PowerShell pour StorSimple pour terminer l’installation initiale de votre appareil StorSimple, comme expliqué dans la procédure suivante. Vous devez utiliser un logiciel d’émulation de terminal pour effectuer cette étape. Pour plus d’informations, consultez la rubrique [Utilisation de PuTTY pour se connecter à la console série de l’appareil](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov.md)]

## Étape 4 : Fin de l’installation minimale de l’appareil

Pour pouvoir mener à bien la configuration minimale de votre appareil StorSimple, vous devez :

- configurer le serveur DNS secondaire ;
- activer la norme iSCSI sur au moins une interface réseau ;
- affecter des adresses IP fixes aux deux contrôleurs.

Pour obtenir la configuration minimale requise, procédez comme suit dans Microsoft Azure Government Portal.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## Étape 5 : Création d’un conteneur de volumes

Un conteneur de volumes dispose de paramètres de compte de stockage, de bande passante et de chiffrement pour tous les volumes qu’il contient. Vous devez créer un conteneur de volumes avant de commencer la configuration des volumes sur votre appareil StorSimple.

Procédez comme suit pour créer un conteneur de volumes dans Microsoft Azure Government Portal.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Étape 6 : Création d’un volume

Après avoir créé un conteneur de volumes, vous pouvez configurer un volume de stockage sur l’appareil StorSimple pour vos serveurs. Procédez comme suit pour créer un volume dans Microsoft Azure Government Portal.

> [AZURE.IMPORTANT]Azure StorSimple peut créer uniquement des volumes alloués dynamiquement. Vous ne pouvez pas créer de volumes entièrement ou partiellement alloués sur un système Azure StorSimple.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## Étape 7 : Montage, initialisation et formatage d’un volume

Procédez comme suit sur votre hôte Windows Server.

> [AZURE.IMPORTANT]

> - Pour une haute disponibilité de votre solution StorSimple, nous vous recommandons de configurer MPIO sur vos serveurs hôtes (facultatif) avant de configurer iSCSI. La configuration de MPIO sur les serveurs hôtes permet de s'assurer que les serveurs peuvent tolérer une panne de liaison, de réseau ou d'interface.

> - Pour obtenir les instructions d’installation et de configuration de MPIO et d’iSCSI sur l’hôte Windows Server, consultez [Configuration de MPIO pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md). Ces instructions incluent également les étapes pour monter, initialiser et formater des volumes StorSimple.

> - Pour obtenir les instructions d’installation et de configuration de MPIO et d’iSCSI sur un hôte Linux, consultez [Configuration de MPIO pour votre hôte Linux StorSimple](storsimple-configure-mpio-linux.md).

Si vous décidez de ne pas configurer MPIO, procédez comme suit pour monter, initialiser et formater vos volumes StorSimple sur un hôte Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Étape 8 : Sauvegarde

Les sauvegardes fournissent une protection jusqu’à une date et une heure des volumes et optimisent la récupération tout en réduisant les délais de restauration. Vous pouvez effectuer deux types de sauvegarde sur votre appareil StorSimple : les instantanés locaux et les instantanés cloud. Chacun de ces types de sauvegarde peut être **Planifié** ou **Manuel**.

Procédez comme suit pour créer une sauvegarde planifiée dans Microsoft Azure Government Portal.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Vous pouvez à tout moment effectuer une sauvegarde manuelle. Pour connaître les procédures à suivre, consultez la rubrique [Création d’une sauvegarde manuelle](#create-a-manual-backup).

## Configuration d’un nouveau compte de stockage pour le service

Il s’agit d’une étape facultative que vous devez exécuter uniquement si vous n’avez pas activé la création automatique d’un compte de stockage avec votre service. Un compte de stockage Microsoft Azure est requis pour créer un conteneur de volumes StorSimple.

Si vous devez créer un compte de stockage Azure dans une autre région, consultez la page [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md) pour obtenir des instructions détaillées.

Procédez comme suit sur la page **Service StorSimple Manager**, dans Microsoft Azure Government Portal.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## Utilisation de PuTTY pour se connecter à la console série de l’appareil

Pour vous connecter à Windows PowerShell pour StorSimple, vous devez utiliser un logiciel d’émulation de terminal tel que PuTTY. Vous pouvez utiliser PuTTY lorsque vous accédez à l’appareil directement via la console série ou en ouvrant une session telnet à partir d’un ordinateur distant.

[AZURE.INCLUDE [Utilisation de PuTTY pour se connecter à la console série de l’appareil](../../includes/storsimple-use-putty.md)]

## Recherche et application des mises à jour

La mise à jour de votre appareil peut prendre plusieurs heures. Procédez comme suit pour rechercher et appliquer des mises à jour sur votre appareil.

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### Mise à jour de votre appareil

1.	Sur la page **Démarrage rapide** de l’appareil, cliquez sur **Appareils**. Sélectionnez l’appareil physique, cliquez sur **Maintenance** puis sur **Rechercher les mises à jour**.  
2.	La tâche créée recherche les mises à jour disponibles. Si des mises à jour sont disponibles, l’option **Rechercher les mises à jour** devient **Installer les mises à jour**. Cliquez sur **Installer les mises à jour**. 
3.	Une tâche de mise à jour est créée. Surveillez l’état de la mise à jour en accédant à **Tâches**.

	> [AZURE.NOTE]Lorsque la tâche de mise à jour démarre, elle affiche immédiatement l'état de 50 %. L’état ne passe à 100 % que lorsque la tâche de mise à jour est terminée. Il n’existe aucun état en temps réel du processus de mise à jour.

4.	Lorsque l'appareil est correctement mis à jour, activez les interfaces réseau Data 2 et Data 3 si elles ont été désactivées.

## Obtention du nom qualifié d’un hôte Windows Server

Procédez comme suit pour obtenir le nom qualifié iSCSI d’un hôte Windows exécutant Windows Server® 2012.

[AZURE.INCLUDE [Création d’une sauvegarde manuelle](../../includes/storsimple-get-iqn.md)]

## Création d’une sauvegarde manuelle

Procédez comme suit, dans Microsoft Azure Government Portal, pour créer une sauvegarde manuelle à la demande pour un seul volume, sur votre appareil StorSimple.

[AZURE.INCLUDE [Création d’une sauvegarde manuelle](../../includes/storsimple-create-manual-backup-gov.md)]

## Configuration de solution MPIO

La solution MPIO (Multipath I/O) est une fonctionnalité facultative qui n’est pas installée sur Windows Server par défaut. Il doit être installé en tant que fonctionnalité via le Gestionnaire de serveur. Pour obtenir les instructions d’installation de la solution MPIO, consultez la rubrique [Configuration de la solution MPIO pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md).

Pour obtenir les instructions d’installation de MPIO pour un appareil StorSimple connecté à un hôte Linux, consultez [Configuration de MPIO pour votre hôte Linux](storsimple-configure-mpio-linux.md).

> [AZURE.NOTE]La solution MPIO n’est pas prise en charge sur un appareil virtuel StorSimple.

## Étapes suivantes

Configuration d’un [appareil virtuel](storsimple-virtual-device.md).

Utilisez le [service StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) pour gérer votre appareil StorSimple.
 

<!---HONumber=AcomDC_1217_2015-->