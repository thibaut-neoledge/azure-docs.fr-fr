---
title: "Administration du service StorSimple Manager | Microsoft Docs"
description: "Découvrez comment gérer votre appareil StorSimple à l&quot;aide du service StorSimple Manager dans le portail Azure Classic."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2586582e-d85c-42e1-afb3-be734c1c0461
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d5d5aa67d14d2344c58e67ee78ea3f2b5d8fd415


---
# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>Utiliser le service StorSimple Manager pour gérer votre appareil StorSimple
## <a name="overview"></a>Vue d'ensemble
Cet article décrit l’interface de service StorSimple Manager, y compris la connexion à ce service, les options disponibles et les liens vers des flux de travail spécifiques exécutables via cette interface utilisateur. Ce guide s’applique aux appareils StorSimple physiques comme virtuels.

Cet article portera sur les éléments suivants :

* Connexion au service StorSimple Manager
* Navigation dans l’interface utilisateur de StorSimple Manager
* Gestion de votre appareil StorSimple via le service StorSimple Manager

## <a name="connect-to-storsimple-manager-service"></a>Connexion au service StorSimple Manager
Le service StorSimple Manager s’exécute dans Microsoft Azure et se connecte à plusieurs appareils StorSimple. Vous devez utiliser un portail Microsoft Azure Classic s'exécutant dans un navigateur pour gérer ces appareils. Pour vous connecter au service StorSimple Manager, procédez comme suit.

#### <a name="to-connect-to-the-service"></a>Connexion au service :
1. Accédez à [https://manage.windowsazure.com/](https://manage.windowsazure.com/).
2. À l’aide de votre compte Microsoft, connectez-vous au portail Microsoft Azure Classic (situé dans l’angle supérieur droit du volet).
3. Faites défiler le volet de navigation de gauche jusqu’au service StorSimple Manager.

## <a name="navigate-storsimple-manager-service-ui"></a>Navigation dans l’interface utilisateur du service StorSimple Manager
La hiérarchie de navigation de l’interface utilisateur du service StorSimple Manager figure dans le tableau ci-après.

* **StorSimple Manager** permet d'atteindre les pages de niveau de service de l’interface utilisateur applicables à tous les appareils au sein d'un service.
* **Appareils** vous redirige vers les pages d'interface utilisateur de niveau appareil applicables à un appareil spécifique.
* **Conteneurs de volumes** vous redirige vers la page affichant tous les volumes associés à un appareil.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Hiérarchie de navigation du service StorSimple Manager
| Page d’accueil | Pages de niveau de service | Pages de niveau appareil | Pages de niveau appareil |
| --- | --- | --- | --- |
| service StorSimple Manager |Tableau de bord du service |Page du tableau de bord d’un appareil | |
| Appareils → |Surveiller | | |
| Catalogue de sauvegarde |Conteneurs de volume → |Volumes | |
| Configurer (Service) |Stratégies de sauvegarde | | |
| Travaux |Configurer (Appareil) | | |
| Alertes |Maintenance | | |

![Vidéo disponible](./media/storsimple-manager-service-administration/Video_icon.png) **Vidéo disponible**

Pour visionner une vidéo qui vous guide à travers l’interface utilisateur du service StorSimple Manager, cliquez [ici](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>Gestion d’un appareil StorSimple via le service StorSimple Manager
Le tableau suivant récapitule toutes les tâches de gestion courantes et les flux de travail complexes pouvant être effectués dans l'interface utilisateur du service StorSimple Manager. L’organisation de ces tâches dépend des pages de l'interface utilisateur sur lesquelles elles sont basées.

Pour plus d'informations sur chaque flux de travail, cliquez sur la procédure appropriée dans le tableau.

#### <a name="storsimple-manager-workflows"></a>Flux de travail de StorSimple Manager
| Pour ce faire... | Accédez à cette page de l'interface utilisateur... | Suivez cette procédure. |
| --- | --- | --- |
| Création d’un service</br>Supprimer un service</br>Obtenir la clé d’inscription au service</br>Régénération d’une clé d’inscription de service |service StorSimple Manager |[Déploiement du service StorSimple Manager](storsimple-manage-service.md) |
| Modification de la clé de chiffrement des données de service</br>Affichage des journaux des opérations |Service StorSimple Manager → Tableau de bord |[Utilisation du tableau de bord du service StorSimple Manager](storsimple-service-dashboard.md) |
| Désactivation d’un appareil</br>Suppression d’un appareil |Service StorSimple Manager → Appareil |[Désactivation ou suppression d’un appareil](storsimple-deactivate-and-delete-device.md) |
| En savoir plus sur le basculement entre appareils et la récupération d’urgence</br>Basculement vers un appareil physique</br>Basculement vers un appareil virtuel</br>Continuité d’activité et récupération d’urgence (Business Continuity Disaster Recovery - BCDR) |Service StorSimple Manager → Appareil |[Basculement et récupération d’urgence pour votre appareil StorSimple](storsimple-device-failover-disaster-recovery.md) |
| Liste des sauvegardes d’un volume</br>Sélectionner un jeu de sauvegarde</br>Suppression d’un jeu de sauvegarde |Service StorSimple Manager → Catalogue de sauvegarde |[Gestion des sauvegardes](storsimple-manage-backup-catalog.md) |
| Clonage d’un volume |Service StorSimple Manager → Catalogue de sauvegarde |[Clonage d’un volume](storsimple-clone-volume.md) |
| Restauration d'un jeu de sauvegarde |Service StorSimple Manager → Catalogue de sauvegarde |[Restauration d'un jeu de sauvegarde](storsimple-restore-from-backup-set.md) |
| À propos des comptes de stockage</br>Ajout d’un compte de stockage</br>Modification d’un compte de stockage</br>Suppression d'un compte de stockage</br>Rotation des clés des comptes de stockage. |Service StorSimple Manager → Configuration |[Gestion des comptes de stockage](storsimple-manage-storage-accounts.md) |
| À propos des modèles de bande passante</br>Ajouter un modèle de bande passante</br>Modifier un modèle de bande passante</br>Supprimer un modèle de bande passante</br>Utiliser un modèle de bande passante par défaut</br>Création d’un modèle de bande passante à la journée commençant à une heure précise |Service StorSimple Manager → Configuration |[Modèles de bande passante](storsimple-manage-bandwidth-templates.md) |
| À propos des enregistrements de contrôle d’accès</br>Création d'un enregistrement de contrôle d’accès</br>Modifier un enregistrement de contrôle d’accès</br>Supprimer un enregistrement de contrôle d’accès |Service StorSimple Manager → Configuration |[Gestion d’enregistrements de contrôle d’accès](storsimple-manage-acrs.md) |
| Affichage des détails d’une tâche</br>Annulation d’un travail |Service StorSimple Manager → Tâches |[Gestion des travaux](storsimple-manage-jobs.md) |
| Réception de notifications d’alerte</br>Gérer les alertes</br>Consulter les alertes |Service StorSimple Manager → Alertes |[Affichage et gestion des alertes StorSimple](storsimple-manage-alerts.md) |
| Affichage des initiateurs connectés</br>Recherche du numéro de série de l’appareil</br>Recherche de l’IQN cible |Service StorSimple Manager → Appareils → Tableau de bord |[Utilisation du tableau de bord d’un appareil StorSimple](storsimple-device-dashboard.md) |
| Création de graphiques d’analyse |Service StorSimple Manager → Appareils → Surveillance |[Surveillance de votre appareil StorSimple](storsimple-monitor-device.md) |
| Ajout d’un conteneur de volumes</br>Modifier un conteneur de volumes</br>Suppression d’un conteneur de volumes |Service StorSimple Manager → Appareils → Conteneurs de volume |[Gestion de conteneurs de volume](storsimple-manage-volume-containers.md) |
| Ajout d’un volume</br>Modification d’un volume</br>Mise hors connexion d’un volume</br>Suppression d’un volume</br>Analyse d’un volume |Service StorSimple Manager → Appareils → Conteneurs de volume → Volumes |[Gestion de volumes](storsimple-manage-volumes.md) |
| Modification des paramètres de l’appareil</br>Modification des paramètres d’heure</br>Modification des paramètres DNS.md</br>Configuration d’interfaces réseau |Service StorSimple Manager → Appareils → Configuration |[Modification de la configuration de votre appareil StorSimple](storsimple-modify-device-config.md) |
| Affichage des paramètres de proxy web |Service StorSimple Manager → Appareils → Configuration |[Configuration du proxy web de votre appareil](storsimple-configure-web-proxy.md) |
| Modification du mot de passe administrateur de votre appareil</br>Modification du mot de passe de StorSimple Snapshot Manager |Service StorSimple Manager → Appareils → Configuration |[Modification des mots de passe StorSimple](storsimple-change-passwords.md) |
| Configuration de la gestion à distance |Service StorSimple Manager → Appareils → Configuration |[Connexion à distance à votre appareil StorSimple](storsimple-remote-connect.md) |
| Configuration des paramètres d'alerte |Service StorSimple Manager → Appareils → Configuration |[Affichage et gestion des alertes StorSimple](storsimple-manage-alerts.md) |
| Configuration de CHAP pour votre appareil StorSimple |Service StorSimple Manager → Appareils → Configuration |[Configuration de CHAP pour votre appareil StorSimple](storsimple-configure-chap.md) |
| Ajout d’une stratégie de sauvegarde</br>Ajouter ou modifier une planification</br>Supprimer une stratégie de sauvegarde</br>Exécuter une sauvegarde manuelle</br>Création d’une stratégie de sauvegarde personnalisée avec plusieurs volumes et planifications |Service StorSimple Manager → Appareils → Stratégies de sauvegarde |[Gestion des stratégies de sauvegarde](storsimple-manage-backup-policies.md) |
| Arrêt des contrôleurs d’appareil</br>Redémarrage des contrôleurs de l’appareil</br>Arrêt des contrôleurs de l'appareil</br>Réinitialisation de votre appareil aux valeurs par défaut</br>(Les données ci-dessus s’appliquent aux appareils en local uniquement) |Service StorSimple Manager → Appareils → Maintenance |[Gestion du contrôleur d’appareil StorSimple](storsimple-manage-device-controller.md) |
| En savoir plus sur les composants matériels de StorSimple</br>Surveillance de l'état du matériel</br>(Les données ci-dessus s’appliquent aux appareils en local uniquement) |Service StorSimple Manager → Appareils → Maintenance |[Surveillance des composants matériels](storsimple-monitor-hardware-status.md) |
| Création d’un package de prise en charge |Service StorSimple Manager → Appareils → Maintenance |[Création et gestion d’un package de prise en charge](storsimple-create-manage-support-package.md) |
| Installer les mises à jour logicielles |Service StorSimple Manager → Appareils → Maintenance |[Mise à jour de votre appareil](storsimple-update-device.md) |

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes de fonctionnement quotidien de votre appareil StorSimple ou avec l’un de ses composants matériels, consultez la rubriques suivantes :

* [Résolution des problèmes d’un appareil opérationnel](storsimple-troubleshoot-operational-device.md)
* [Utilisation des indicateurs de surveillance de StorSimple](storsimple-monitoring-indicators.md)

Si vous ne parvenez pas à résoudre les problèmes et vous devez générer une demande d’intervention, consultez [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).




<!--HONumber=Nov16_HO3-->


