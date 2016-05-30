<properties 
   pageTitle="Administration du Gestionnaire d’instantanés StorSimple | Microsoft Azure"
   description="Fournit une vue d’ensemble et des liens vers d’autres informations sur les tâches et les flux de travail d’administration du Gestionnaire d’instantanés StorSimple."
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
   ms.date="05/18/2016"
   ms.author="v-sharos" />

# Utiliser le Gestionnaire d’instantanés StorSimple pour gérer votre solution StorSimple

## Vue d'ensemble

Gestionnaire d’instantanés StorSimple est un composant logiciel enfichable Microsoft Management Console (MMC) qui simplifie la protection des données et la gestion des sauvegardes dans l’environnement Microsoft Azure StorSimple. Avec le Gestionnaire d’instantanés StorSimple, vous pouvez gérer les données Microsoft Azure StorSimple hébergées dans le centre de données et dans le cloud sous forme de solution de stockage intégrée unique et réduire ainsi la complexité des processus de sauvegarde et les coûts.

La console de gestion centralisée du Gestionnaire d’instantanés StorSimple vous permet de créer des copies instantanées cohérentes des données locales et du cloud. Par exemple, vous pouvez utiliser la console pour :

- Configurer, sauvegarder et supprimer des volumes.

- Configurer des groupes de volumes pour garantir que les données sauvegardées sont cohérentes pour l’application.

- Gérer les stratégies de sauvegarde afin que les données soient sauvegardées selon un calendrier prédéterminé.

- Créer des copies indépendantes des données, qui peuvent être stockées dans le cloud et utilisées pour la récupération d’urgence.

Cet article fournit des liens vers des didacticiels qui décrivent le Gestionnaire d’instantanés StorSimple et comment l’utiliser pour exécuter des tâches et des flux de travail d’administration système complets.

- Pour plus d’informations sur l’architecture et les composants du Gestionnaire d’instantanés StorSimple, consultez l’article [Qu’est-ce que le Gestionnaire d’instantanés StorSimple ?](storsimple-what-is-snapshot-manager.md) 

- Pour télécharger le Gestionnaire d’instantanés StorSimple, accédez à [la page de téléchargement du Gestionnaire d’instantanés StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

- Pour connaître les procédures de déploiement du Gestionnaire d’instantanés StorSimple, consultez l’article [Déployer le Gestionnaire d’instantanés StorSimple](storsimple-snapshot-manager-deployment.md).

>[AZURE.NOTE] Vous ne pouvez pas utiliser le Gestionnaire d'instantanés StorSimple pour gérer les baies virtuelles Microsoft Azure StorSimple (également appelées appareils virtuels locaux StorSimple).

## Tâches et flux de travail du Gestionnaire d’instantanés StorSimple

Vous pouvez utiliser le Gestionnaire d’instantanés StorSimple pour surveiller et gérer les tâches de sauvegarde en cours, planifiées et terminées. De plus, le Gestionnaire d’instantanés StorSimple offre un catalogue pouvant comporter jusqu’à 64 sauvegardes terminées. Vous pouvez utiliser ce catalogue pour rechercher et restaurer des volumes ou des fichiers individuels.

| SI VOUS VOULEZ FAIRE CECI... | SUIVEZ CE DIDACTICIEL... |
|:---------------------------|:----------------------|
|En savoir plus sur le Gestionnaire d’instantanés StorSimple | [Qu’est-ce que le Gestionnaire d’instantanés StorSimple ? ](storsimple-what-is-snapshot-manager.md)|
| Installer le Gestionnaire d’instantanés StorSimple<br>Réinstaller le Gestionnaire d’instantanés StorSimple<br>Supprimer le Gestionnaire d’instantanés StorSimple| [Déployer le Gestionnaire d’instantanés StorSimple](storsimple-snapshot-manager-deployment.md) |
| Utiliser les menus et les fonctionnalités du Gestionnaire d’instantanés StorSimple :<ul><li>Barre de menus</li><li>Barre d’outils</li><li>Volet Étendue</li><li>Volet Résultats</li><li>Volet Actions</li><li>Navigation au clavier et touches de raccourci</li></ul>| [Interface utilisateur du Gestionnaire d’instantanés StorSimple](storsimple-use-snapshot-manager.md) |
| Utiliser les fonctionnalités de console MMC courantes incluses dans le Gestionnaire d’instantanés StorSimple :<ul><li>Afficher</li><li>Nouvelle fenêtre</li><li>Actualiser</li><li>Exporter la liste</li><li>Aide</li></ul>| [Utiliser les actions de menu de la console MMC dans le Gestionnaire d’instantanés StorSimple](storsimple-snapshot-manager-mmc-menu.md)
| Ajouter ou remplacer un appareil<br>Connecter un appareil<br>Vérifier les groupes de volumes importés<br>Actualiser les appareils connectés<br>Authentifier un appareil<br>Afficher les détails d’un appareil<br>Supprimer une configuration d’appareil<br>Modifier le mot de passe d’un appareil<br>Remplacer un appareil défaillant<br>| [Utiliser le Gestionnaire d’instantanés StorSimple pour connecter et gérer des appareils StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Monter des volumes<br>Afficher des informations sur les volumes<br>Supprimer un volume<br>Relancer l’analyse des volumes<br>Configurer et sauvegarder un volume de base<br>Configurer et sauvegarder un volume dynamique en miroir| [Utiliser le Gestionnaire d’instantanés StorSimple pour afficher et gérer les volumes](storsimple-snapshot-manager-manage-volumes.md) |
| Afficher les groupes de volumes<br>Créer un groupe de volumes<br>Sauvegarder un groupe de volumes<br>Modifier un groupe de volumes<br>Supprimer un groupe de volumes | [Utiliser le Gestionnaire d’instantanés StorSimple pour créer et gérer des groupes de volumes](storsimple-snapshot-manager-manage-volume-groups.md) |
| Créer une stratégie de sauvegarde<br>Modifier une stratégie de sauvegarde<br>Supprimer une stratégie de sauvegarde | [Utiliser le Gestionnaire d’instantanés StorSimple pour créer et gérer des stratégies de sauvegarde](storsimple-snapshot-manager-manage-backup-policies.md) |
| Afficher et gérer les tâches de sauvegarde planifiés<br>Afficher et gérer les tâches de sauvegarde récentes<br>Afficher et gérer les tâches de sauvegarde en cours d’exécution | [Utiliser le Gestionnaire d’instantanés StorSimple pour afficher et gérer les tâches de sauvegarde](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Restaurer un volume<br>Cloner un volume ou un groupe de volumes<br>Supprimer une sauvegarde<br>Récupérer un fichier<br>Restaurer la base de données du Gestionnaire d’instantanés StorSimple| [Utiliser le Gestionnaire d’instantanés StorSimple pour gérer le catalogue de sauvegarde](storsimple-snapshot-manager-manage-backup-catalog.md) |

## Étapes suivantes

[Télécharger le Gestionnaire d’instantanés StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

<!---HONumber=AcomDC_0518_2016-->