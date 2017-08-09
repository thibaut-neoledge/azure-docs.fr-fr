---
title: "Modèle de données Log Analytics pour la sauvegarde Azure"
description: "Cet article présente des informations détaillées sur le modèle de données Log Analytics pour les données de sauvegarde Azure."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: dfd5c73d-0d34-4d48-959e-1936986f9fc0
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 041a8835a1dd185739b23d4073fd5811bb4490b5
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modèle de données Log Analytics pour les données de sauvegarde Azure
Cet article décrit le modèle de données utilisé pour transmettre des données de rapport à Log Analytics. À l’aide de ce modèle de données, vous pouvez créer des requêtes personnalisées, des tableaux de bord et les utiliser dans OMS. 

## <a name="using-azure-backup-data-model"></a>Utilisation du modèle de données de sauvegarde Azure
Vous pouvez utiliser les champs suivants fournis dans le cadre du modèle de données pour créer des éléments visuels, des requêtes personnalisées et des tableaux de bord, en fonction de vos besoins.

### <a name="alert"></a>Alerte
Ce tableau fournit plus d’informations sur les champs liés aux alertes.

| Champ | Type de données | Description |
| --- | --- | --- |
| AlertUniqueId_s |Texte |ID unique de l’alerte générée |
| AlertType_s |Texte |Type d’alerte généré par exemple, Sauvegarde |
| AlertStatus_s |Texte |État de l’alerte, par exemple, Active |
| AlertOccurenceDateTime_s |Date/Heure |Date et heure de la création de l’alerte |
| AlertSeverity_s |Texte |Gravité de l’alerte, par exemple, Critique |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| BackupItemUniqueId_s |Texte |ID unique de l’élément de sauvegarde auquel appartient cette alerte |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet alerte, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cette alerte |
| Nom d'opération |Texte |Ce champ représente le nom de l’opération en cours - Alerte |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |ID unique de l’élément protégé auquel appartient cette alerte |
| VaultUniqueId_s |Texte |ID unique de l’élément protégé auquel appartient cette alerte |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Ce champ représente l’ID de la ressource pour laquelle les données sont collectées ; affiche l’ID du coffre Recovery Services |
| SubscriptionId |Texte |Ce champ représente l’ID d’abonnement de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Ce champ représente le groupe de ressources de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Ce champ représente le fournisseur de ressources pour lequel les données sont collectées - Microsoft.RecoveryServices |
| ResourceType |Texte |Ce champ représente le type de la ressource pour laquelle les données sont collectées - Coffres |

### <a name="backupitem"></a>BackupItem
Ce tableau fournit plus d’informations sur les champs liés aux éléments de sauvegarde.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texte |ID unique de l’élément de sauvegarde |
| BackupItemId_s |Texte |ID de l’élément de sauvegarde |
| BackupItemName_s |Texte |Nom de l’élément de sauvegarde |
| BackupItemFriendlyName_s |Texte |Nom convivial de l’élément de sauvegarde |
| BackupItemType_s |Texte |Type d’élément de sauvegarde, par exemple, Machine virtuelle, Dossier de fichiers |
| ProtectedServerName_s |Texte |Nom du serveur protégé auquel l’élément de sauvegarde appartient |
| ProtectionState_s |Texte |État de protection actuel de l’élément de sauvegarde par exemple, Protégé, Protection arrêtée |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet élément de sauvegarde, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cet élément de sauvegarde |
| Nom d'opération |Texte |Ce champ représente le nom de l’opération en cours - Élément de sauvegarde |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Ce champ représente l’ID de la ressource pour laquelle les données sont collectées ; affiche l’ID du coffre Recovery Services |
| SubscriptionId |Texte |Ce champ représente l’ID d’abonnement de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Ce champ représente le groupe de ressources de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Ce champ représente le fournisseur de ressources pour lequel les données sont collectées - Microsoft.RecoveryServices |
| ResourceType |Texte |Ce champ représente le type de la ressource pour laquelle les données sont collectées - Coffres |

### <a name="backupitemassociation"></a>BackupItemAssociation
Ce tableau fournit des détails sur les associations d’éléments de sauvegarde avec différentes entités.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texte |ID unique de l’élément de sauvegarde |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet association d’éléments de sauvegarde, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cet élément de sauvegarde |
| Nom d'opération |Texte |Ce champ représente le nom de l’opération en cours - Association d’éléments de sauvegarde |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| PolicyUniqueId_g |Texte |ID unique permettant d’identifier la stratégie à laquelle l’élément de sauvegarde est associé |
| ProtectedServerUniqueId_s |Texte |ID unique du serveur protégé auquel l’élément de sauvegarde appartient |
| VaultUniqueId_s |Texte |ID unique du coffre auquel l’élément de sauvegarde appartient |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Ce champ représente l’ID de la ressource pour laquelle les données sont collectées ; affiche l’ID du coffre Recovery Services |
| SubscriptionId |Texte |Ce champ représente l’ID d’abonnement de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Ce champ représente le groupe de ressources de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Ce champ représente le fournisseur de ressources pour lequel les données sont collectées - Microsoft.RecoveryServices |
| ResourceType |Texte |Ce champ représente le type de la ressource pour laquelle les données sont collectées - Coffres |

### <a name="job"></a>Travail
Ce tableau fournit plus d’informations sur les champs liés aux travaux.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| BackupItemUniqueId_s |Texte |ID unique de l’élément de sauvegarde auquel appartient ce travail |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet travail, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient ce travail |
| Nom d'opération |Texte |Ce champ représente le nom de l’opération en cours - Travail |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |ID unique de l’élément protégé auquel appartient ce travail |
| VaultUniqueId_s |Texte |ID unique de l’élément protégé auquel appartient ce travail |
| JobOperation_s |Texte |Opération pour laquelle le travail est exécuté, par exemple, Sauvegarde, Restauration, Configuration de sauvegarde |
| JobStatus_s |Texte |État du travail terminé par exemple, Terminé, Échec |
| JobFailureCode_s |Texte |Chaîne de Code d’échec en raison du type d’échec de travail survenu |
| JobStartDateTime_s |Date/Heure |Date et heure de démarrage de l’exécution du travail |
| BackupStorageDestination_s |Texte |Destination du stockage de sauvegarde, par exemple, Cloud, Disque  |
| JobDurationInSecs_s | Number |Durée totale de la tâche en secondes |
| DataTransferredInMB_s | Number |Données transférées en Mo pour ce travail|
| JobUniqueId_g |Texte |ID unique d’identification du travail |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Ce champ représente l’ID de la ressource pour laquelle les données sont collectées ; affiche l’ID du coffre Recovery Services |
| SubscriptionId |Texte |Ce champ représente l’ID d’abonnement de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Ce champ représente le groupe de ressources de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Ce champ représente le fournisseur de ressources pour lequel les données sont collectées - Microsoft.RecoveryServices |
| ResourceType |Texte |Ce champ représente le type de la ressource pour laquelle les données sont collectées - Coffres |

### <a name="policy"></a>Stratégie
Ce tableau fournit plus d’informations sur les champs liés à la stratégie.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet stratégie, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cette stratégie |
| Nom d'opération |Texte |Ce champ représente le nom de l’opération en cours - Stratégie |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| PolicyUniqueId_g |Texte |ID unique d’identification de la stratégie |
| PolicyName_s |Texte |Nom de la stratégie définie |
| BackupFrequency_s |Texte |Fréquence à laquelle les sauvegardes sont exécutées, par exemple, Quotidienne, Hebdomadaire |
| BackupTimes_s |Texte |Date et heure auxquelles les sauvegardes sont planifiées |
| BackupDaysOfTheWeek_s |Texte |Jours de la semaine auxquels les sauvegardes sont planifiées |
| RetentionDuration_s |Nombre entier |Durée de rétention des sauvegardes configurées |
| DailyRetentionDuration_s |Nombre entier |Durée totale de rétention en jours des sauvegardes configurées |
| DailyRetentionTimes_s |Texte |Date et heure auxquelles la rétention quotidienne est configurée |
| WeeklyRetentionDuration_s |Nombre décimal |Durée totale de rétention hebdomadaire en semaines des sauvegardes configurées |
| WeeklyRetentionTimes_s |Texte |Date et heure auxquelles la rétention hebdomadaire est configurée |
| WeeklyRetentionDaysOfTheWeek_s |Texte |Jours de la semaine sélectionnés pour la rétention hebdomadaire |
| MonthlyRetentionDuration_s |Nombre décimal |Durée totale de rétention en mois des sauvegardes configurées |
| MonthlyRetentionTimes_s |Texte |Date et heure auxquelles la rétention mensuelle est configurée |
| MonthlyRetentionFormat_s |Texte |Type de configuration de la rétention mensuelle, par exemple, quotidienne pour une configuration quotidienne, hebdomadaire pour une configuration hebdomadaire |
| MonthlyRetentionDaysOfTheWeek_s |Texte |Jours de la semaine sélectionnés pour la rétention mensuelle |
| MonthlyRetentionWeeksOfTheMonth_s |Texte |Semaines du mois au cours desquelles la rétention mensuelle est configurée, par exemple, Première, Dernière, etc. |
| YearlyRetentionDuration_s |Nombre décimal |Durée totale de rétention en années des sauvegardes configurées |
| YearlyRetentionTimes_s |Texte |Date et heure auxquelles la rétention annuelle est configurée |
| YearlyRetentionMonthsOfTheYear_s |Texte |Mois de l’année sélectionnés pour la rétention annuelle |
| YearlyRetentionFormat_s |Texte |Type de configuration de la rétention annuelle, par exemple, quotidienne pour une configuration quotidienne, hebdomadaire pour une configuration hebdomadaire |
| YearlyRetentionDaysOfTheMonth_s |Texte |Dates du mois sélectionnées pour la rétention annuelle |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Ce champ représente l’ID de la ressource pour laquelle les données sont collectées ; affiche l’ID du coffre Recovery Services |
| SubscriptionId |Texte |Ce champ représente l’ID d’abonnement de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Ce champ représente le groupe de ressources de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Ce champ représente le fournisseur de ressources pour lequel les données sont collectées - Microsoft.RecoveryServices |
| ResourceType |Texte |Ce champ représente le type de la ressource pour laquelle les données sont collectées - Coffres |

### <a name="policyassociation"></a>PolicyAssociation
Ce tableau fournit des détails sur les associations de stratégies avec différentes entités.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet stratégie, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cette stratégie |
| Nom d'opération |Texte |Ce champ représente le nom de l’opération en cours - Association de stratégies |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| PolicyUniqueId_g |Texte |ID unique d’identification de la stratégie |
| VaultUniqueId_s |Texte |ID unique du coffre auquel cette stratégie appartient |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Ce champ représente l’ID de la ressource pour laquelle les données sont collectées ; affiche l’ID du coffre Recovery Services |
| SubscriptionId |Texte |Ce champ représente l’ID d’abonnement de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Ce champ représente le groupe de ressources de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Ce champ représente le fournisseur de ressources pour lequel les données sont collectées - Microsoft.RecoveryServices |
| ResourceType |Texte |Ce champ représente le type de la ressource pour laquelle les données sont collectées - Coffres |

### <a name="protectedserver"></a>ProtectedServer
Ce tableau fournit plus d’informations sur les champs liés au serveur protégé.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| ProtectedServerName_s |Texte |Nom du serveur protégé |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet serveur protégé, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient ce serveur protégé |
| Nom d'opération |Texte |Ce champ représente le nom de l’opération en cours - Serveur protégé |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |ID unique du serveur protégé |
| RegisteredContainerId_s |Texte |ID du conteneur inscrit pour la sauvegarde |
| ProtectedServerType_s |Texte |Type de serveur protégé sauvegardé, par exemple, Windows |
| ProtectedServerFriendlyName_s |Texte |Nom convivial du serveur protégé |
| AzureBackupAgentVersion_s |Texte |Numéro de version de l’agent Backup |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Ce champ représente l’ID de la ressource pour laquelle les données sont collectées ; affiche l’ID du coffre Recovery Services |
| SubscriptionId |Texte |Ce champ représente l’ID d’abonnement de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Ce champ représente le groupe de ressources de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Ce champ représente le fournisseur de ressources pour lequel les données sont collectées - Microsoft.RecoveryServices |
| ResourceType |Texte |Ce champ représente le type de la ressource pour laquelle les données sont collectées - Coffres |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Ce tableau fournit des détails sur les associations de serveurs protégés avec d’autres entités.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet association de serveurs protégés, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient ce serveur protégé |
| Nom d'opération |Texte |Ce champ représente le nom de l’opération en cours - Association de serveurs protégés |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |ID unique du serveur protégé |
| VaultUniqueId_s |Texte |ID unique du coffre auquel ce serveur protégé appartient |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Ce champ représente l’ID de la ressource pour laquelle les données sont collectées ; affiche l’ID du coffre Recovery Services |
| SubscriptionId |Texte |Ce champ représente l’ID d’abonnement de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Ce champ représente le groupe de ressources de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Ce champ représente le fournisseur de ressources pour lequel les données sont collectées - Microsoft.RecoveryServices |
| ResourceType |Texte |Ce champ représente le type de la ressource pour laquelle les données sont collectées - Coffres |

### <a name="storage"></a>Storage
Ce tableau fournit plus d’informations sur les champs liés au stockage.

| Champ | Type de données | Description |
| --- | --- | --- |
| CloudStorageInBytes_s |Nombre décimal |Stockage des sauvegardes dans le cloud utilisé par les sauvegardes, calculé en fonction de la dernière valeur |
| ProtectedInstances_s |Nombre décimal |Nombre d’instances protégées utilisées pour calculer le stockage frontal dans la facturation, calculé en fonction de la dernière valeur |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet stockage, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient ce stockage |
| Nom d'opération |Texte |Ce champ représente le nom de l’opération en cours - Stockage |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |ID unique du serveur protégé pour lequel le stockage est calculé |
| VaultUniqueId_s |Texte |ID unique du coffre pour lequel le stockage est calculé |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Ce champ représente l’ID de la ressource pour laquelle les données sont collectées ; affiche l’ID du coffre Recovery Services |
| SubscriptionId |Texte |Ce champ représente l’ID d’abonnement de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Ce champ représente le groupe de ressources de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Ce champ représente le fournisseur de ressources pour lequel les données sont collectées - Microsoft.RecoveryServices |
| ResourceType |Texte |Ce champ représente le type de la ressource pour laquelle les données sont collectées - Coffres |

### <a name="vault"></a>Coffre
Ce tableau fournit plus d’informations sur les champs liés aux coffres.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V1** |
| State_s |Texte |État actuel de l’objet coffre, par exemple, Actif, Supprimé |
| Nom d'opération |Texte |Ce champ représente le nom de l’opération en cours - Coffre |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| VaultUniqueId_s |Texte |ID unique du coffre |
| VaultName_s |Texte |Nom du coffre |
| AzureDataCenter_s |Texte |Centre de données dans lequel se trouve le coffre |
| StorageReplicationType_s |Texte |Type de réplication de stockage pour le coffre, par exemple, Géoredondant |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Ce champ représente l’ID de la ressource pour laquelle les données sont collectées ; affiche l’ID du coffre Recovery Services |
| SubscriptionId |Texte |Ce champ représente l’ID d’abonnement de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Ce champ représente le groupe de ressources de la ressource (coffre RS) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Ce champ représente le fournisseur de ressources pour lequel les données sont collectées - Microsoft.RecoveryServices |
| ResourceType |Texte |Ce champ représente le type de la ressource pour laquelle les données sont collectées - Coffres |

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous passez en revue le modèle de données de création de rapports de sauvegarde d’Azure, vous pouvez commencer à [créer des tableaux de bord](../log-analytics/log-analytics-dashboards.md) dans Log Analytics et OMS.

