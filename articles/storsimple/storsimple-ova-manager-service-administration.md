<properties 
   pageTitle="Administration de StorSimple Manager Virtual Array | Microsoft Azure"
   description="Découvrez comment gérer votre StorSimple Virtual Array local à l’aide du service StorSimple Manager dans le portail Azure Classic."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="v-sharos" />

# Utiliser le service StorSimple Manager pour gérer votre StorSimple Virtual Array (version préliminaire)

![flux du processus d'installation](./media/storsimple-ova-manager-service-administration/manage4.png)

## Vue d’ensemble

Cet article décrit l’interface de service StorSimple Manager, y compris la connexion à ce service et les différentes options disponibles, et fournit les liens vers des flux de travail spécifiques exécutables par le biais de cette interface utilisateur.

À la fin de cet article, vous serez en mesure d’effectuer les opérations suivantes :

- Se connecter au service StorSimple Manager
- Navigation dans l’interface utilisateur de StorSimple Manager
- Gestion de votre StorSimple Virtual Array par le biais du service StorSimple Manager

> [AZURE.NOTE] Pour afficher les options de gestion disponibles pour l’appareil StorSimple série 8000, consultez [Utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

## Se connecter au service StorSimple Manager

Le service StorSimple Manager s’exécute dans Microsoft Azure et se connecte à plusieurs StorSimple Virtual Arrays. Vous devez utiliser un portail Microsoft Azure Classic s'exécutant dans un navigateur pour gérer ces appareils. Pour vous connecter au service StorSimple Manager, procédez comme suit.

#### Connexion au service :

1. Accédez à [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. À l’aide de votre compte Microsoft, connectez-vous au portail Microsoft Azure Classic (situé dans l’angle supérieur droit du volet).

3. Faites défiler le volet de navigation de gauche jusqu’au service StorSimple Manager.

    ![Faites défiler jusqu’au service](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## Naviguer dans l’interface utilisateur du service StorSimple Manager

La hiérarchie de navigation de l’interface utilisateur du service StorSimple Manager figure dans le tableau ci-après.

- La page d’accueil de **StorSimple Manager** permet d’atteindre les pages de niveau de service de l’interface utilisateur applicables à tous les Virtual Arrays au sein d’un service.

- La page **Appareils** vous redirige vers les pages d’interface utilisateur de niveau appareil applicables à un Virtual Array spécifique.

#### Hiérarchie de navigation du service StorSimple Manager

|Page d’accueil|Pages de niveau de service|Pages de niveau appareil|
|---|---|---|
|Service StorSimple Manager|Tableau de bord (service)|Tableau de bord (appareil)|
|Appareils →|Surveiller|
|Catalogue de sauvegarde|Partages (serveur de fichiers) ou </br>Volumes (serveur iSCSI)|
|Configurer (service)|Configurer (appareil)|
|Travaux|Maintenance|
|Alertes|

## Utiliser le service StorSimple Manager pour effectuer des tâches de gestion

Le tableau suivant récapitule toutes les tâches de gestion courantes et les flux de travail complexes pouvant être effectués dans l'interface utilisateur du service StorSimple Manager. L’organisation de ces tâches dépend des pages de l'interface utilisateur sur lesquelles elles sont basées.

Pour plus d'informations sur chaque flux de travail, cliquez sur la procédure appropriée dans le tableau.

#### Flux de travail de StorSimple Manager

|Pour ce faire...|Accédez à cette page de l'interface utilisateur...|Suivez cette procédure|
|---|---|---|
|Création d’un service</br>Suppression d’un service</br>Obtention de la clé d’inscription de service</br>Régénération de la clé d’inscription de service|Service StorSimple Manager|[Déployer le service StorSimple Manager](storsimple-ova-manage-service.md)|
|Désactivation d’un Virtual Array</br>Suppression d’un Virtual Array|Service StorSimple Manager → Appareil|[Désactiver ou supprimer un Virtual Array](storsimple-ova-deactivate-and-delete-device.md)|
|Récupération d’urgence et basculement entre appareils</br>Configuration requise pour le basculement</br>Basculement vers un appareil virtuel</br>Récupération BCDR</br>Erreurs pendant une récupération d’urgence|Service StorSimple Manager → Appareil|[Basculement d'appareil et récupération d'urgence pour votre StorSimple Virtual Array](storsimple-ova-failover-dr.md)|
|Sauvegarder des partages et des volumes</br>Exécuter une sauvegarde manuelle</br>Modifier la planification de la sauvegarde</br>Afficher les sauvegardes existantes|Service StorSimple Manager → Catalogue de sauvegarde|[Sauvegarder votre StorSimple Virtual Array](storsimple-ova-backup.md)|
|Restaurer des partages à partir d’un jeu de sauvegarde</br>Restaurer des volumes à partir d’un jeu de sauvegarde</br>Récupération au niveau élément (serveur de fichiers uniquement)|Service StorSimple Manager → Catalogue de sauvegarde|[Restaurer à partir d’une sauvegarde de votre StorSimple Virtual Array](storsimple-ova-restore.md)|
|À propos des comptes de stockage</br>Ajout d’un compte de stockage</br>Modification d’un compte de stockage</br>Suppression d’un compte de stockage|Service StorSimple Manager → Configuration|[Gérer les comptes de stockage pour le StorSimple Virtual Array](storsimple-ova-manage-storage-accounts.md)|
|À propos des enregistrements de contrôle d’accès</br>Ajout ou modification d’un enregistrement de contrôle d’accès</br>Suppression d’un enregistrement de contrôle d’accès|Service StorSimple Manager → Configuration|[Gérer les enregistrements de contrôle d’accès pour le StorSimple Virtual Array](storsimple-ova-manage-acrs.md)|
|Configuration des paramètres d’alerte</br>Réception de notifications d’alerte</br>Gestion d’alertes</br>Consultation d’alertes|Service StorSimple Manager → Alertes|[Afficher et gérer les alertes pour le StorSimple Virtual Array](storsimple-ova-manage-alerts.md)
|Modification du mot de passe d’administrateur de l’appareil|Service StorSimple Manager → Appareils → Configuration|[Modifier le mot de passe administrateur de l’appareil StorSimple Virtual Array](storsimple-ova-change-device-admin-password.md)|
|Installer les mises à jour logicielles|Service StorSimple Manager → Appareils → Maintenance|[Mettre à jour votre Virtual Array](storsimple-ova-update.md)|

>[AZURE.NOTE] Vous devez utiliser [l’interface utilisateur web locale](storsimple-ova-web-ui-admin.md) pour les tâches suivantes :
>
>- [Récupération de la clé de chiffrement des données de service](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Création d’un package de prise en charge](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Arrêt et redémarrage d’un Virtual Array](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##Étapes suivantes
Pour plus d’informations sur l’interface utilisateur web et sur son utilisation, accédez à [Utiliser l’interface utilisateur web de StorSimple pour gérer votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0218_2016-->