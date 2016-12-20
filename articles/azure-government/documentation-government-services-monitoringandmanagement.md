---
title: "Surveillance et gestion d’Azure Government | Microsoft Docs"
description: "Cette article propose une comparaison de fonctionnalités et des conseils pour le développement d’applications pour Azure Government."
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/31/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 722c53f819126791073575da04eded2ec5465764
ms.openlocfilehash: 9d4ba4eff1c9768a11c18cbf531b252d767bc9f2


---
# <a name="azure-government-monitoring-management"></a>Surveillance et gestion d’Azure Government
Cet article décrit les variantes et considérations relatives aux services de surveillance et de gestion pour l’environnement Azure Government.

## <a name="automation"></a>Automatisation
L’automatisation est généralement disponible dans Azure Government.

### <a name="variations"></a>Variantes
Les fonctionnalités d’automatisation suivantes ne sont pas actuellement disponibles dans Azure Government.

* Création d’une information d’identification de principal du service pour l’authentification

Pour plus d’informations, voir la [documentation publique sur l’automatisation](../automation/automation-intro.md).

## <a name="backup"></a>Backup 
Backup est mis à la disposition générale dans Azure Government.

Pour plus d’informations, consultez [Azure Government Backup](documentation-government-services-backup.md).

### <a name="variations"></a>Variantes
Actuellement, les fonctionnalités Backup suivantes ne sont pas disponibles dans Azure Government :

* Coffres Azure Resource Manager
* Gestion à l’aide du portail Azure (portail Azure Classic pris en charge)

## <a name="log-analytics"></a>Log Analytics
Log Analytics est mis à la disposition générale dans Azure Government.

### <a name="variations"></a>Variantes
Les fonctionnalités et solutions suivantes de Log Analytics ne sont actuellement pas disponibles dans Azure Government.

* Solutions en version d’évaluation dans Microsoft Azure, notamment :
  * Solution de surveillance du réseau
  * Solution Application Dependency Monitor
  * Solution Office 365
  * Solution d’analytique de mise à niveau de Windows 10
  * Solution Application Insights
  * Solution d’analytique du réseau Azure
  * Solution d’analytique d’Azure Automation
  * Solution d’analytique de Key Vault
* Solutions et fonctionnalités qui nécessitent des mises à jour de logiciels locaux, notamment :
  * Intégration avec System Center Operations Manager 2016 (les versions antérieures d’Operations Manager sont prises en charge)
  * Groupes d’ordinateurs de System Center Configuration Manager
  * Solution Surface Hub
* Fonctionnalités en version préliminaire publique d’Azure, notamment :
  * Exportation de données vers Power BI
* Métriques Azure et diagnostics Azure
* Applications mobiles de Operations Management Suite

Les URL de Log Analytics sont différentes dans Azure Government :

| Azure (public) | Azure Government | Remarques |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Portail Log Analytics |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[API de collecte de données](../log-analytics/log-analytics-data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |Communication avec l’agent - [configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |Communication avec l’agent - [configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |Communication avec l’agent - [configuration des paramètres de pare-feu](../log-analytics/log-analytics-proxy-firewall.md) |

Les fonctionnalités Log Analytics suivantes ont un comportement différent dans Azure Government :

* L’agent Windows doit être téléchargé à partir du [portail Log Analytics](https://oms.microsoft.us) pour Azure Government.
* Pour connecter votre serveur d’administration System Center Operations Manager à Log Analytics, vous devez télécharger et importer les packs d’administration mis à jour.
  1. Téléchargez et enregistrez les [packs d’administration mis à jour](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Décompressez le fichier que vous avez téléchargé.
  3. Importez les packs d’administration dans Operations Manager. Pour plus d’informations sur l’importation d’un pack d’administration à partir d’un disque, voir [Comment faire pour importer un pack d’administration Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) sur le site web Microsoft TechNet.
  4. Pour connecter Operations Manager à Log Analytics, procédez de la manière décrite dans [Connecter Operations Manager à Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="frequently-asked-questions"></a>Forum Aux Questions
* Puis-je migrer des données de Log Analytics dans Microsoft Azure vers Azure Government ?
  * Non. Il n’est pas possible de déplacer des données ou votre espace de travail de Microsoft Azure vers Azure Government.
* Puis-je passer de l’espace de travail de Microsoft Azure à celui d’Azure Government et inversement à partir du portail Log Analytics d’Operations Management Suite Log Analytics ?
  * Non. Les portails pour Microsoft Azure et Azure Government sont distincts et ne partagent pas d’informations.

Pour plus d’informations, consultez [Documentation de la version publique de Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="site-recovery"></a>Site Recovery
Site Recovery est mis à la disposition générale dans Azure Government.

Pour plus d’informations, consultez la [documentation de la version publique de Site Recovery](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Variantes
Actuellement, les fonctionnalités Site Recovery suivantes ne sont pas disponibles dans Azure Government :

* Coffres de récupération de sites Azure Resource Manager

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government. </a>



<!--HONumber=Nov16_HO3-->


