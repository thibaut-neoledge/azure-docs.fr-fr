<properties
   pageTitle="Services et régions pris en charge pour Resource Manager | Microsoft Azure"
   description="Décrit les fournisseurs de ressources qui prennent en charge Resource Manager ainsi que les régions pouvant héberger les ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/13/2015"
   ms.author="tomfitz"/>

# Prise en charge d’Azure Resource Manager pour les services et les régions

Azure Resource Manager vous offre un nouveau moyen de déployer et gérer les services qui composent vos applications. La plupart des services (mais pas tous) offrent une prise en charge complète ou partielle de Resource Manager. Microsoft prévoit d’activer Resource Manager pour les principaux services dans les solutions futures. En attendant que ce processus soit terminé, il est important pour vous de connaître l’état actuel de la prise en charge pour chaque service. Cette rubrique répertorie les fournisseurs de ressources pris en charge pour Azure Resource Manager.

Quand vous déployez vos ressources, vous avez également besoin de connaître les régions qui prennent en charge ces ressources. La section [Régions prises en charge](#supported-regions) vous explique comment trouver les régions dans lesquelles vous pourrez utiliser votre abonnement et vos ressources.

Les tableaux suivants indiquent si les services répertoriés prennent en charge ou non le déploiement et la gestion via Resource Manager. La colonne intitulée **Déplacer des ressources** indique si les ressources du type en question peuvent être déplacées vers un nouveau groupe de ressources et un nouvel abonnement. La colonne intitulée **Portail en version préliminaire** indique si vous pouvez créer le service par le biais du portail en version préliminaire.


## Calcul

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| Virtual Machines | Oui | Oui | Non | [Créer une machine virtuelle](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| Batch | Oui | Non | | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | |
| Dynamics Lifecycle Services | Oui | Non | | | |
| Virtual Machines (classic) | Limité | Non | Partiel (voir ci-dessous) | - | - | | Remote App | Non | - | - | - | - | | Service Fabric | Non | - | - | - | - |

Virtual Machines (classic) fait référence à des ressources qui ont été déployées via le modèle de déploiement classique, et non via le modèle de déploiement Resource Manager. En général, ces ressources ne prennent pas en charge les opérations de Resource Manager, sauf certaines opérations qui ont été activées. Pour plus d’informations sur les modèles de déploiement, consultez [Présentation du déploiement Resource Manager et du déploiement classique](resource-manager-deployment-model.md).

Les ressources Virtual Machines (classic) peuvent être déplacées vers un nouveau groupe de ressources, mais pas vers un nouvel abonnement.

## Web et mobilité

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| API Management| Oui | Non | Oui | [Créer une API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| API Apps | Oui | Oui | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| Web Apps | Oui | Oui | Oui, avec des limitations (voir ci-dessous) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| Notification Hubs | Oui | Oui | | [Créer un hub de notification](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| Logic Apps | Oui | Oui | | | |
| Mobile Engagements | Oui | Non | Oui | | |

Lorsque vous travaillez avec des applications web, vous ne pouvez pas déplacer uniquement un plan App Service. Pour déplacer des applications web, les options disponibles sont :

- Déplacer toutes les ressources d'un groupe de ressources à un autre, si le groupe de ressources de destination n'a pas déjà de ressources Microsoft.Web.
- Déplacer les applications web vers un autre groupe de ressources, mais conserver le plan App Service dans le groupe de ressources d'origine.


## Données et stockage

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB | Oui | Oui | Oui | [REST DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| Storage | Oui | Oui | | [Créer un stockage](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Storage.json) |
| Cache Redis | Oui | Oui | Oui | | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| SQL Database | Oui | Oui | Oui | [Créer une base de données](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| Search | Oui | Oui | Oui | [REST Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| SQL Data Warehouse | Oui | Oui | | | |
| StorSimple | Non | Non | - | - | - | | Backup | Non | Non | - | - | - | | Site Recovery | Non | Non | - | - | - | | Managed cache | Non | Non | - | - | - | | Data Catalog | Non | Non | - | - | - |

## Analyse

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Event Hub | Oui | Non | | [Créer un hub d’événements](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| Stream Analytics | Oui | Oui | | | |
| HDInsights | Oui | Oui | | | |
| Data Factory | Oui | Oui | Oui | [Créer une fabrique de données](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| Machine Learning | Non | Non | - | - | - |

## Mise en réseau

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Application Gateway | Oui | | | | |
| DNS | Oui | | | [Créer une zone DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Load Balancer | Oui | | | [Créer un équilibrage de charge](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Virtual Network | Oui | Oui | Non | [Créer un réseau virtuel](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Traffic Manager | Oui | Non | | [Créer un profil Traffic Manager](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| Express Route | Non | Non | - | - | - |

## Média et CDN

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Media Service | Non | Non | | | |
| CDN | Non | Non | | | |

## Intégration hybride

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| BizTalk Services | Oui | Non | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Service Bus | Oui | Non | | [REST Service Bus](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |

## Gestion des identités et des accès 

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | Non | Non | - | - | - | | Azure Actice Directory B2C | Non | Non | - | - | - | | Multi-Factor Authentication | Non | Non | - | - | - |

## Services de développement 

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | Oui | Oui | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Bing Maps | Oui | Oui | | | |
| Visual Studio Account | Oui | | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## Gestion 

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Automation | Oui | Oui | | | |
| Key Vault | Oui | Non | Oui | [REST Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| Scheduler | Oui | Non | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Operational Insights | Oui | Non | Oui | | |
| IoTHubs | Oui | Oui | | | |


## Régions prises en charge

Quand vous déployez des ressources, vous devez généralement spécifier une région pour les ressources. Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource. Ces limitations peuvent être liées à la fiscalité appliquée dans votre pays de domiciliation, ou le résultat d’une stratégie mise en place par votre administrateur d’abonnement pour restreindre les régions disponibles.

Avant de déployer vos ressources, vérifiez quelles régions sont prises en charge pour votre type de ressource en exécutant l’une des commandes suivantes.

### API REST

La meilleure façon de découvrir quelles régions sont disponibles pour un type de ressource particulier est d’utiliser l’opération [Liste de tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx). Cette opération affiche uniquement les régions disponibles pour votre abonnement et votre type de ressource.

### PowerShell

L’exemple suivant montre comment obtenir la liste des régions prises en charge pour les sites web utilisant Azure PowerShell 1.0 en version préliminaire. Pour plus d’informations sur Azure PowerShell 1.0 en version préliminaire, consultez [Azure PowerShell 1.0 en version préliminaire](https://azure.microsoft.com/blog/azps-1-0-pre/).

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
La sortie doit ressembler à ceci :

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### Interface de ligne de commande Azure

L’exemple suivant affiche la liste de tous les emplacements pris en charge pour chaque type de ressource.

    azure location list

Vous pouvez également filtrer les emplacements répertoriés à l’aide d’un outil tel que **jq**. Pour en savoir plus sur les outils comme jq, consultez [Outils utiles pour interagir avec Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Résultat :

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

<!---HONumber=Oct15_HO3-->