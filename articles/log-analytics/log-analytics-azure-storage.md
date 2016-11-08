---
title: Présentation de la collecte de données de stockage Azure dans Log Analytics | Microsoft Docs
description: Les ressources Azure peuvent écrire des journaux et des métriques dans un compte de stockage Azure, souvent en utilisant des diagnostics Azure. Log Analytics peut indexer ces données et les rendre détectables.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Présentation de la collecte de données de stockage Azure dans Log Analytics
De nombreuses ressources Azure peuvent écrire des journaux et des métriques dans un compte de stockage Azure. Log Analytics peut utiliser ces données et faciliter l’analyse de vos ressources Azure.

Pour écrire dans Stockage Azure, une ressource peut utiliser les diagnostics Azure, ou avoir sa propre manière d’écrire des données. Ces données peuvent être écrites sous différents formats dans l’un des emplacements suivants :

* Table Azure
* Objet blob Azure
* Event Hub

Log Analytics prend en charge les services Azure qui écrivent des données en utilisant des [journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). De plus, Log Analytics prend en charge d’autres services qui génèrent des journaux et des métriques dans différents formats et emplacements.  

> [!NOTE]
> Vous êtes facturé aux tarifs de données Azure normaux pour le stockage et les transactions lorsque vous envoyez des diagnostics à un compte de stockage et lorsque Log Analytics lit les données de votre compte de stockage.
> 
> 

![Diagramme du stockage Azure](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Ressources Azure prises en charge
Log Analytics peut collecter des données pour les ressources Azure suivantes :

| Type de ressource | Journaux (catégories de diagnostic) | Solution Log Analytics |
| --- | --- | --- |
| Application Insights |Availability <br> Événements personnalisés <br> Exceptions <br> Requêtes <br> |Application Insights (version préliminaire) |
| API Management | |*Aucune* (version préliminaire) |
| Automatisation <br> Microsoft.Automation/AutomationAccounts |JobLogs <br> JobStreams |AzureAutomation (version préliminaire) |
| Key Vault <br> Microsoft.KeyVault/vaults |AuditEvent |KeyVault (version préliminaire) |
| Application Gateway <br> Microsoft.Network/ApplicationGateways |ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog |AzureNetworking (version préliminaire) |
| Groupe de sécurité réseau <br> Microsoft.Network/NetworkSecurityGroups |NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter |AzureNetworking (version préliminaire) |
| Service Fabric |ETWEvent <br> Événement opérationnel <br> Événement Reliable Actor <br> Événement de service fiable |ServiceFabric (version préliminaire) |
| Machines virtuelles |Syslog Linux <br> Événement Windows <br> Journal IIS <br> ETWEvent Windows |*Aucune* |
| Rôles web <br> Rôles de travail |Syslog Linux <br> Événement Windows <br> Journal IIS <br> ETWEvent Windows |*Aucune* |

> [!NOTE]
> Pour l’analyse des machines virtuelles (Linux et Windows), nous vous recommandons d’installer l’[extension de machine virtuelle Log Analytics](log-analytics-azure-vm-extension.md). L’agent fournit des informations sur vos machines virtuelles, plus approfondies que si vous utilisez les diagnostics écrits dans le stockage.
> 
> 

Vous pouvez nous aider à hiérarchiser d’autres journaux devant être analysés par OMS en votant sur notre [page de commentaires](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).

* Voir [Analyser les journaux de diagnostic Azure à l’aide de Log Analytics](log-analytics-azure-storage-json.md) pour en savoir plus sur la manière dont Log Analytics peut lire les journaux des services Azure qui prennent en charge les [journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) :
  * coffre de clés Azure
  * Azure Automation
  * Application Gateway
  * groupes de sécurité réseau ;
* Voir [Utiliser un Stockage Blob pour IIS et un Stockage Table pour les événements](log-analytics-azure-storage-iis-table.md) pour en savoir plus sur la manière dont Log Analytics peut lire les journaux pour les services Azure qui écrivent des diagnostics dans le Stockage Table ou des journaux IIS écrits dans le Stockage Blob, notamment :
  * Service Fabric
  * Rôles web
  * Rôles de travail
  * Machines virtuelles

Application Insights est disponible en version préliminaire limitée, et utilise une exportation continue vers le Stockage Blob. Pour participer à la version préliminaire privée, contactez l’équipe de votre compte Microsoft ou consultez les détails sur le [site dédié aux commentaires](https://feedback.azure.com/forums/267889-log-analytics/suggestions/6519248-integration-with-app-insights).

## <a name="next-steps"></a>Étapes suivantes
* [Analyser les journaux de diagnostic Azure à l’aide de Log Analytics](log-analytics-azure-storage-json.md) pour lire les journaux des services Azure qui écrivent des diagnostics dans le Stockage Blob au format JSON.
* [Utiliser un Stockage Blob pour IIS et un Stockage Table pour les événements](log-analytics-azure-storage-iis-table.md) afin de lire les journaux pour les services Azure qui écrivent des diagnostics dans Stockage Table ou les journaux IIS écrits dans le Stockage Blob.
* [Activer les solutions](log-analytics-add-solutions.md) pour fournir des informations sur les données.
* [Utiliser les requêtes de recherche](log-analytics-log-searches.md) pour analyser les données.

<!--HONumber=Oct16_HO2-->


