---
title: Documentation Azure Government | Microsoft Docs
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: Azure-Government
cloud: gov
documentationcenter: ''
author: scooxl
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/05/2016
ms.author: scooxl

---
# <a name="azure-government-management-and-security"></a>Sécurité et gestion d’Azure Government
## <a name="key-vault"></a>Key Vault
Pour plus d’informations sur ce service et son utilisation, consultez la <a href="https://azure.microsoft.com/documentation/services/key-vault">documentation publique d’Azure Key Vault. </a>

### <a name="data-considerations"></a>Considérations sur les données
Les informations suivantes identifient les limites d’Azure Government pour Azure Key Vault :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
| --- | --- |
| Toutes les données chiffrées avec une clé Azure Key Vault peuvent contenir des données contrôlées/réglementées. |Les métadonnées Azure Key Vault n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre coffre de clés.  N’entrez pas de données réglementées/contrôlées dans les champs suivants : Noms de groupes de ressources, Noms de coffres de clés, Nom d’abonnement |

Azure Key Vault est mis à la disposition générale dans Azure Government. Comme en environnement public, il n’y a pas d’extension. Key Vault est donc uniquement disponible via PowerShell et l’interface CLI.

## <a name="log-analytics"></a>Log Analytics
Log Analytics est mis à la disposition générale dans Azure Government. 

### <a name="differences-from-public-azure"></a>Différences par rapport à la version publique d’Azure
Les fonctionnalités et solutions suivantes de Log Analytics ne sont actuellement pas disponibles dans Azure Government. Cette liste est mise à jour en cas de modification de l’état des fonctions / solutions.

* Solutions en version préliminaire dans la version publique d’Azure, notamment :
  * Solution de surveillance du réseau
  * Solution d’analytique du réseau Azure
  * Solution Office 365
  * Solution d’Analytique de mise à niveau de Windows 10
  * Surveillance Application Dependency Monitor
  * Application Insights
  * Journaux d’activité Azure
  * Analytique Azure Automation
  * Analytique Key Vault
* Solutions et fonctionnalités qui nécessitent Azure Automation, notamment :
  * Gestion des mises à jour
  * Gestion des changements
  * Alertes qui déclenchent un runbook Azure Automation
* Solutions et fonctionnalités qui nécessitent la mise à jour des logiciels locaux, notamment
  * Intégration avec System Center Operations Manager 2016
  * Groupes d’ordinateurs de System Center Configuration Manager
  * Solution Surface Hub
* Fonctionnalités en version préliminaire dans la version publique d’Azure, notamment
  * Exportation des données vers Power BI
* Intégration du Portail Azure
  * La sélection des comptes de stockage Azure à surveiller doit être effectuée avec des modèles PowerShell ou Resource Manager
  * La sélection des machines virtuelles pour activer l’agent Log Analytics doit être effectuée avec des modèles PowerShell ou Resource Manager
  * Métriques Azure et diagnostics Azure
* Applications mobiles OMS
* Extension de machine virtuelle OMS Linux Agent
* Données d'utilisation

Les fonctionnalités Log Analytics suivantes ont un comportement différent dans Azure Government :

* L’agent Windows doit être téléchargé à partir du [portail Log Analytics](https://oms.microsoft.us) pour Azure Government.
* Le chargement de données à l’aide de l’API Collecteur de données requiert l’utilisation de l’URL d’Azure Government, https://*idEspaceDeTravail*.ods.opinsights.azure.us, où *idEspaceDeTravail* est l’ID de l’espace de travail du portail OMS. 
* Pour connecter votre serveur d’administration System Center Operations Manager à Log Analytics, vous devez télécharger et importer les packs d’administration mis à jour.
  1. Téléchargez et enregistrez les [packs d’administration mis à jour](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Décompressez le fichier que vous avez téléchargé.
  3. Importez les packs d’administration dans Operations Manager. Pour plus d’informations sur l’importation d’un pack d’administration à partir d’un disque, consultez la rubrique [Importation d’un pack d’administration Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) sur le site web Microsoft TechNet.
  4. Pour connecter Operations Manager à Log Analytics, suivez les étapes de [Connecter Operations Manager à Log Analytics](../log-analytics/log-analytics-om-agents.md). 

### <a name="frequently-asked-questions"></a>Forum Aux Questions
* Puis-je migrer des données de Log Analytics dans la version publique d’Azure vers Azure Government ?
  * Non. Il n’est pas possible de déplacer des données ou votre espace de travail de la version publique d’Azure vers Azure Government.
* Puis-je passer de l’espace de travail de la version publique d’Azure à celui d’Azure Government et inversement à partir du portail OMS Log Analytics ?
  * Non. Le portail de la version publique d’Azure et celui d’Azure Government sont distincts et ne partagent pas d’informations. 

Pour plus d’informations, consultez [Documentation de la version publique de Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government. </a>

<!--HONumber=Oct16_HO2-->


