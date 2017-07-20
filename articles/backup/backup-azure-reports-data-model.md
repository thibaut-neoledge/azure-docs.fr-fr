---
title: "Modèle de données pour la sauvegarde Azure"
description: "Cet article présente des informations détaillées sur le modèle de données Power BI pour les rapports de sauvegarde Azure."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 0767c330-690d-474d-85a6-aa8ddc410bb2
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/26/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: efecbc9f1c410744f49795889c4ec3cc618f07e0
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017


---
# <a name="data-model-for-azure-backup-reports"></a>Modèle de données pour les rapports de sauvegarde Azure
Cet article décrit le modèle de données Power BI utilisé pour créer des rapports de sauvegarde Azure. À l’aide de ce modèle de données, vous pouvez filtrer les rapports existants sur les champs appropriés et surtout, créer vos propres rapports en utilisant des tables et des champs du modèle. 

## <a name="creating-new-reports-in-power-bi"></a>Création de rapports dans Power BI
Power BI fournit des fonctionnalités de personnalisation qui vous permettent de [créer des rapports en utilisant le modèle de données](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Utilisation du modèle de données de sauvegarde Azure
Vous pouvez utiliser les champs suivants fournis dans le cadre du modèle de données pour créer des rapports et personnaliser des rapports existants.

### <a name="alert"></a>Alerte
Ce tableau présente les champs de base et les agrégations des différents champs liés aux alertes.

| Champ | Type de données | Description |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Nombre entier |Nombre d’alertes créées dans l’intervalle de temps sélectionné |
| %ActiveAlertsCreatedInPeriod |Pourcentage |Pourcentage d’alertes actives dans l’intervalle de temps sélectionné |
| %CriticalAlertsCreatedInPeriod |Pourcentage |Pourcentage d’alertes critiques dans l’intervalle de temps sélectionné |
| AlertOccurenceDate |Date |Date de création de l’alerte |
| AlertSeverity |Texte |Gravité de l’alerte, par exemple, Critique |
| AlertStatus |Texte |État de l’alerte, par exemple, Active |
| AlertType |Texte |Type d’alerte généré par exemple, Sauvegarde |
| AlertUniqueId |Texte |ID unique de l’alerte générée |
| AsOnDateTime |Date/Heure |Dernière heure d’actualisation de la ligne sélectionnée |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Nombre décimal |Temps moyen (en minutes) de résolution de l’alerte pour l’intervalle de temps sélectionné |
| EntityState |Texte |État actuel de l’objet alerte, par exemple, Actif, Supprimé |

### <a name="backup-item"></a>Élément de sauvegarde
Ce tableau présente les champs de base et les agrégations des différents champs liés à l’élément de sauvegarde.

| Champ | Type de données | Description |
| --- | --- | --- |
| #BackupItems |Nombre entier |Nombre d’éléments de sauvegarde |
| #UnprotectedBackupItems |Nombre entier |Nombre d’éléments de sauvegarde arrêtés à des fins de protection ou configurés pour les sauvegardes, mais dont les sauvegardes n’ont pas démarré|
| AsOnDateTime |Date/Heure |Dernière heure d’actualisation de la ligne sélectionnée |
| BackupItemFriendlyName |Texte |Nom convivial de l’élément de sauvegarde |
| BackupItemId |Texte |ID de l’élément de sauvegarde |
| BackupItemName |Texte |Nom de l’élément de sauvegarde |
| BackupItemType |Texte |Type d’élément de sauvegarde, par exemple, Machine virtuelle, Dossier de fichiers |
| EntityState |Texte |État actuel de l’objet élément de sauvegarde, par exemple, Actif, Supprimé |
| LastBackupDateTime |Date/Heure |Heure de la dernière sauvegarde de l’élément de sauvegarde sélectionné |
| LastBackupState |Texte |État de la dernière sauvegarde de l’élément de sauvegarde sélectionné par exemple, Réussite, Échec |
| LastSuccessfulBackupDateTime |Date/Heure |Heure de la dernière sauvegarde réussie de l’élément de sauvegarde sélectionné |
| ProtectionState |Texte |État de protection actuel de l’élément de sauvegarde par exemple, Protégé, Protection arrêtée |

### <a name="calendar"></a>Calendrier
Ce tableau fournit plus d’informations sur les champs liés au calendrier.

| Champ | Type de données | Description |
| --- | --- | --- |
| Date |Date |Date sélectionnée pour le filtrage des données |
| DateKey |Texte |Clé unique pour chaque élément de date |
| DayDiff |Nombre décimal |Écart en jours pour filtrer les données par exemple, 0 indique des données du jour en cours, -1 indique des données du jour précédent, 0 et -1 indiquent des données du jour en cours et précédent  |
| Mois |Texte |Mois de l’année sélectionné pour filtrer les données, le mois commence le premier jour et se termine le 31e jour |
| MonthDate | Date |Date à laquelle le mois se termine, sélectionnée pour filtrer les données |
| MonthDiff |Nombre décimal |Écart en mois du filtrage des données par exemple, 0 indique des données du mois en cours, -1 indique des données du mois précédent, 0 et -1 indiquent des données du mois en cours et précédent |
| Semaine |Texte |Semaine sélectionnée pour filtrer les données, la semaine commence le dimanche et se termine le samedi |
| WeekDate |Date |Date à laquelle la semaine se termine, sélectionnée pour filtrer les données |
| WeekDiff |Nombre décimal |Écart en semaines pour filtrer les données par exemple, 0 indique des données de la semaine en cours, -1 indique des données de la semaine précédente, 0 et -1 indiquent des données de la semaine en cours et précédente |
| Year |Texte |Année du calendrier sélectionnée pour filtrer les données |
| YearDate |Date |Date à laquelle l’année se termine, sélectionnée pour filtrer les données |

### <a name="job"></a>Travail
Ce tableau présente les champs de base et les agrégations des différents champs liés aux travaux.

| Champ | Type de données | Description |
| --- | --- | --- |
| #JobsCreatedInPeriod |Nombre entier |Nombre de travaux créés dans l’intervalle de temps sélectionné |
| %FailuresForJobsCreatedInPeriod |Pourcentage |Pourcentage global d’échec des travaux dans l’intervalle de temps sélectionné |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Nombre décimal |Valeur au 80e centile des données transférées en Mo pour les travaux de **sauvegarde** créés dans l’intervalle de temps sélectionné |
| AsOnDateTime |Date/Heure |Dernière heure d’actualisation de la ligne sélectionnée |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Nombre décimal |Temps moyen en minutes pour les travaux de **sauvegarde terminés** créés dans l’intervalle de temps sélectionné |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Nombre décimal |Temps moyen en minutes pour les travaux de **restauration terminés** créés dans l’intervalle de temps sélectionné |
| BackupStorageDestination |Texte |Destination du stockage de sauvegarde, par exemple, Cloud, Disque  |
| EntityState |Texte |État actuel de l’objet travail, par exemple, Actif, Supprimé |
| JobFailureCode |Texte |Chaîne de Code d’échec en raison du type d’échec de travail survenu |
| JobOperation |Texte |Opération pour laquelle le travail est exécuté, par exemple, Sauvegarde, Restauration, Configuration de sauvegarde |
| JobStartDate |Date |Date de démarrage de l’exécution du travail |
| JobStartTime |Temps |Heure de démarrage de l’exécution du travail |
| JobStatus |Texte |État du travail terminé par exemple, Terminé, Échec |
| JobUniqueId |Texte |ID unique d’identification du travail |

### <a name="policy"></a>Stratégie
Ce tableau présente les champs de base et les agrégations des différents champs liés aux stratégies.

| Champ | Type de données | Description |
| --- | --- | --- |
| #Policies |Nombre entier |Nombre de stratégies de sauvegarde qui existent dans le système |
| #PoliciesInUse |Nombre entier |Nombre de stratégies actuellement utilisées pour la configuration des sauvegardes |
| AsOnDateTime |Date/Heure |Dernière heure d’actualisation de la ligne sélectionnée |
| BackupDaysOfTheWeek |Texte |Jours de la semaine auxquels les sauvegardes sont planifiées |
| BackupFrequency |Texte |Fréquence à laquelle les sauvegardes sont exécutées, par exemple, Quotidienne, Hebdomadaire |
| BackupTimes |Texte |Date et heure auxquelles les sauvegardes sont planifiées |
| DailyRetentionDuration |Nombre entier |Durée totale de rétention en jours des sauvegardes configurées |
| DailyRetentionTimes |Texte |Date et heure auxquelles la rétention quotidienne est configurée |
| EntityState |Texte |État actuel de l’objet stratégie, par exemple, Actif, Supprimé |
| MonthlyRetentionDaysOfTheMonth |Texte |Dates du mois sélectionnées pour la rétention mensuelle |
| MonthlyRetentionDaysOfTheWeek |Texte |Jours de la semaine sélectionnés pour la rétention mensuelle |
| MonthlyRetentionDuration |Nombre décimal |Durée totale de rétention en mois des sauvegardes configurées |
| MonthlyRetentionFormat |Texte |Type de configuration de la rétention mensuelle, par exemple, quotidienne pour une configuration quotidienne, hebdomadaire pour une configuration hebdomadaire |
| MonthlyRetentionTimes |Texte |Date et heure auxquelles la rétention mensuelle est configurée |
| MonthlyRetentionWeeksOfTheMonth |Texte |Semaines du mois au cours desquelles la rétention mensuelle est configurée, par exemple, Première, Dernière, etc. |
| PolicyName |Texte |Nom de la stratégie définie |
| PolicyUniqueId |Texte |ID unique d’identification de la stratégie |
| RetentionType |Texte |Type de stratégie de rétention par exemple, Quotidienne, Hebdomadaire, Mensuelle, Annuelle |
| WeeklyRetentionDaysOfTheWeek |Texte |Jours de la semaine sélectionnés pour la rétention hebdomadaire |
| WeeklyRetentionDuration |Nombre décimal |Durée totale de rétention hebdomadaire en semaines des sauvegardes configurées |
| WeeklyRetentionTimes |Texte |Date et heure auxquelles la rétention hebdomadaire est configurée |
| YearlyRetentionDaysOfTheMonth |Texte |Dates du mois sélectionnées pour la rétention annuelle |
| YearlyRetentionDaysOfTheWeek |Texte |Jours de la semaine sélectionnés pour la rétention annuelle |
| YearlyRetentionDuration |Nombre décimal |Durée totale de rétention en années des sauvegardes configurées |
| YearlyRetentionFormat |Texte |Type de configuration de la rétention annuelle, par exemple, quotidienne pour une configuration quotidienne, hebdomadaire pour une configuration hebdomadaire |
| YearlyRetentionMonthsOfTheYear |Texte |Mois de l’année sélectionnés pour la rétention annuelle |
| YearlyRetentionTimes |Texte |Date et heure auxquelles la rétention annuelle est configurée |
| YearlyRetentionWeeksOfTheMonth |Texte |Semaines du mois au cours desquelles la rétention annuelle est configurée, par exemple, Première, Dernière, etc. |

### <a name="protected-server"></a>Serveur protégé
Ce tableau fournit les champs de base et les agrégations des différents champs liés au serveur protégé.

| Champ | Type de données | Description |
| --- | --- | --- |
| #ProtectedServers |Nombre entier |Nombre de serveurs protégés |
| AsOnDateTime |Date/Heure |Dernière heure d’actualisation de la ligne sélectionnée |
| AzureBackupAgentOSType |Texte |Type de système d’exploitation de l’agent Azure Backup |
| AzureBackupAgentOSVersion |Texte |Version de système d’exploitation de l’agent Azure Backup |
| AzureBackupAgentUpdateDate |Texte |Date de mise à jour de l’agent Backup |
| AzureBackupAgentVersion |Texte |Numéro de version de l’agent Backup |
| BackupManagementType |Texte |Type de fournisseur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| EntityState |Texte |État actuel de l’objet serveur protégé, par exemple, Actif, Supprimé |
| ProtectedServerFriendlyName |Texte |Nom convivial du serveur protégé |
| ProtectedServerName |Texte |Nom du serveur protégé |
| ProtectedServerType |Texte |Type de serveur protégé sauvegardé, par exemple, Conteneur de machine virtuelle IaaS |
| ProtectedServerName |Texte |Nom du serveur protégé auquel l’élément de sauvegarde appartient |
| RegisteredContainerId |Texte |ID du conteneur inscrit pour la sauvegarde |

### <a name="storage"></a>Storage
Ce tableau fournit les champs de base et les agrégations des différents champs liés au stockage.

| Champ | Type de données | Description |
| --- | --- | --- |
| #ProtectedInstances |Nombre décimal |Nombre d’instances protégées utilisées pour calculer le stockage frontal dans la facturation, calculé en fonction de la dernière valeur dans l’intervalle de temps sélectionné |
| AsOnDateTime |Date/Heure |Dernière heure d’actualisation de la ligne sélectionnée |
| CloudStorageInMB |Nombre décimal |Stockage des sauvegardes dans le cloud utilisé par les sauvegardes, calculé en fonction de la dernière valeur dans l’intervalle de temps sélectionné |
| EntityState |Texte |État actuel de l’objet, par exemple, Actif, Supprimé |
| LastUpdatedDate |Date |Date de dernière mise à jour de la ligne sélectionnée |

### <a name="time"></a>Temps
Ce tableau fournit plus d’informations sur les champs liés au temps.

| Champ | Type de données | Description |
| --- | --- | --- |
| Hour |Temps |Heure de la journée, par exemple, 13:00:00 |
| HourNumber |Nombre décimal |Numéro de l’heure dans la journée, par exemple, 13.00 |
| Minute |Nombre décimal |Minute de l’heure |
| PeriodOfTheDay |Texte |Intervalle de temps dans la journée, par exemple, 12-3 |
| Temps |Temps |Heure de la journée, par exemple, 12:00:01 |
| TimeKey |Texte |Valeur de clé représentant l’heure |

### <a name="vault"></a>Coffre
Ce tableau fournit les champs de base et les agrégations des différents champs liés au coffre.

| Champ | Type de données | Description |
| --- | --- | --- |
| #Vaults |Nombre entier |Nombre de coffres |
| AsOnDateTime |Date/Heure |Dernière heure d’actualisation de la ligne sélectionnée |
| AzureDataCenter |Texte |Centre de données dans lequel se trouve le coffre |
| EntityState |Texte |État actuel de l’objet coffre, par exemple, Actif, Supprimé |
| StorageReplicationType |Texte |Type de réplication de stockage pour le coffre, par exemple, Géoredondant |
| SubscriptionId |Texte |ID d’abonnement du client sélectionné pour la génération de rapports |
| VaultName |Texte |Nom du coffre |
| VaultTags |Texte |Balises associées au coffre |

## <a name="next-steps"></a>Étapes suivantes
Lorsque vous examinez le modèle de données de création de rapports de sauvegarde Azure, consultez les articles suivants pour plus d’informations sur la création et l’affichage des rapports dans Power BI.

* [Création de rapports dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrage de rapports dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)

