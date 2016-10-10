<properties
	pageTitle="Documentation Azure Government | Microsoft Azure"
	description="This provides a comparision of features and guidance on developing applications for Azure Government"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="09/23/2016"
	ms.author="ryansoc"/>


#  Sécurité et gestion d’Azure Government

##  Key Vault

Les informations suivantes identifient les limites d’Azure Government pour Azure Key Vault :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données chiffrées avec une clé Azure Key Vault peuvent contenir des données contrôlées/réglementées. | Les métadonnées Azure Key Vault n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre coffre de clés. N’entrez pas de données réglementées/contrôlées dans les champs suivants : Noms de groupes de ressources, Noms de coffres de clés, Nom d’abonnement |

Azure Key Vault est mis à la disposition générale dans Azure Government. Comme en environnement public, il n’y a pas d’extension. Azure Key Vault est donc uniquement disponible via PowerShell et l’interface CLI.

Pour plus d’informations, consultez la [documentation publique d’Azure Key Vault](/key-vault-get-started).

## Log Analytics

Log Analytics est en version préliminaire dans Azure Government.

### Différences par rapport à la version publique d’Azure

Les fonctionnalités et solutions suivantes de Log Analytics ne sont actuellement pas disponibles dans Azure Government. Cette liste est mise à jour en cas de modification de l’état des fonctions / solutions.

+ Métriques en temps quasi-réel
  - Lorsque vous affichez les graphiques des métriques sur une période de moins de six heures, le graphique ne se met pas automatiquement à jour avec les nouvelles valeurs des métriques
+ Exportation des données vers Power BI
+ Solution de surveillance du réseau
+ Solution Office 365
+ Solution d’Analytique de mise à niveau de Windows 10
+ Surveillance Application Dependency Monitor
+ Évaluation des mises à jour
+ Intégration du Portail Azure
  - La sélection des comptes de stockage Azure à surveiller doit être effectuée avec des modèles PowerShell ou Resource Manager
  - La sélection des machines virtuelles pour activer l’agent Log Analytics doit être effectuée avec des modèles PowerShell ou Resource Manager
+ Surveillance Linux
+ Applications mobiles OMS
+ Extension de machine virtuelle Microsoft Monitoring Agent
+ Extension de machine virtuelle OMS Linux Agent
+ Données d'utilisation
+ Messages électroniques d’alerte et correction avec Azure Automation

Les fonctionnalités Log Analytics suivantes ont un comportement différent dans Azure Government :

+ L’agent Windows doit être téléchargé à partir du portail de journal Log Analytics pour Azure Government.
+ La solution de sécurité et d’audit n’a pas de prise en charge de la détection des IP malveillantes.
+ Le chargement de données à l’aide de l’API Collecteur de données requiert l’utilisation de l’URL d’Azure Government.

### Forum Aux Questions

+ Puis-je migrer des données de Log Analytics dans la version publique d’Azure vers Azure Government ?
  - Non. Il n’est pas possible de déplacer des données ou votre espace de travail de la version publique d’Azure vers Azure Government.
+ Puis-je passer de l’espace de travail de la version publique d’Azure à celui d’Azure Government et inversement à partir du portail OMS Log Analytics ?
  - Non. Le portail de la version publique d’Azure et celui d’Azure Government sont distincts et ne partagent pas d’informations.

Pour plus d’informations, consultez [Documentation de la version publique de Log Analytics](../log-analytics/log-analytics-overview.md).

## Étapes suivantes

Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government. </a>

<!---HONumber=AcomDC_0928_2016-->