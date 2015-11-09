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
   ms.date="10/21/2015"
   ms.author="tomfitz"/>

# Prise en charge de Resource Manager pour les services, les régions et les versions API

Azure Resource Manager vous offre un nouveau moyen de déployer et gérer les services qui composent vos applications. La plupart des services (mais pas tous) offrent une prise en charge complète ou partielle de Resource Manager. Microsoft prévoit d’activer Resource Manager pour les principaux services dans les solutions futures. En attendant que ce processus soit terminé, il est important pour vous de connaître l’état actuel de la prise en charge pour chaque service. Cette rubrique répertorie les fournisseurs de ressources pris en charge pour Azure Resource Manager.

Quand vous déployez vos ressources, vous avez également besoin de connaître les régions qui prennent en charge ces ressources et quelles versions d'API sont disponibles pour les ressources. La section [Régions prises en charge](#supported-regions) vous explique comment trouver les régions dans lesquelles vous pourrez utiliser votre abonnement et vos ressources. La section [Versions d'API prises en charge](#supported-api-versions) vous explique comment déterminer les versions d'API que vous pouvez utiliser.

Les tableaux suivants indiquent si les services répertoriés prennent en charge ou non le déploiement et la gestion via Resource Manager. La colonne intitulée **Déplacer des ressources** indique si les ressources du type en question peuvent être déplacées vers un nouveau groupe de ressources et un nouvel abonnement. La colonne intitulée **Portail en version préliminaire** indique si vous pouvez créer le service par le biais du portail en version préliminaire.


## Calcul

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| Virtual Machines | Oui | Oui, de nombreuses options | Non | [Créer une machine virtuelle](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| Batch | Oui | [Oui (classique uniquement)](https://portal.azure.com/#create/Microsoft.BatchAccount) | | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | |
| Dynamics Lifecycle Services | Oui | Non | | | |
| Machines virtuelles (classiques) | Limité | Oui, de nombreuses options | Partiel (voir ci-dessous) | - | - | | Remote App | Non | Non | - | - | - | | Service Fabric | Non | Non | - | - | - |

Machines virtuelles (classiques) fait référence à des ressources qui ont été déployées via le modèle de déploiement classique, et non via le modèle de déploiement Resource Manager. En général, ces ressources ne prennent pas en charge les opérations de Resource Manager, sauf certaines opérations qui ont été activées. Pour plus d’informations sur ces modèles de déploiement, consultez [Présentation du déploiement Resource Manager et du déploiement classique](resource-manager-deployment-model.md).

Les ressources Machines virtuelles (classiques) peuvent être déplacées vers un nouveau groupe de ressources, mais pas vers un nouvel abonnement.

## Web et mobilité

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| API Management | Oui | Non | Oui | [Créer une API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| API Apps | Oui | [Oui](https://portal.azure.com/#create/microsoft_com.ApiApp.0.2.0-preview) | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| Web Apps | Oui | [Oui](https://portal.azure.com/#create/Microsoft.WebSite) | Oui, avec des limitations (voir ci-dessous) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| Notification Hubs | Oui | [Oui](https://portal.azure.com/#create/Microsoft.NotificationHub) | | [Créer un hub de notification](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| Logic Apps | Oui | [Oui](https://portal.azure.com/#create/Microsoft.EmptyWorkflow.0.2.0-preview) | | | |
| Mobile Engagements | Oui | Non | Oui | | |

Lorsque vous travaillez avec des applications web, vous ne pouvez pas déplacer uniquement un plan App Service. Pour déplacer des applications web, les options disponibles sont :

- Déplacer toutes les ressources d'un groupe de ressources à un autre, si le groupe de ressources de destination n'a pas déjà de ressources Microsoft.Web.
- Déplacer les applications web vers un autre groupe de ressources, mais conserver le plan App Service dans le groupe de ressources d'origine.


## Données et stockage

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB | Oui | [Oui](https://portal.azure.com/#create/Microsoft.DocumentDB) | Oui | [REST DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| Storage | Oui | [Oui](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) | | [Créer un stockage](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Storage.json) |
| Cache Redis | Oui | [Oui](https://portal.azure.com/#create/Microsoft.Cache.1.0.4) | Oui | | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| SQL Database | Oui | [Oui](https://portal.azure.com/#create/Microsoft.SQLDatabase.0.5.7-preview) | Oui | [Créer une base de données](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| Search | Oui | [Oui](https://portal.azure.com/#create/Microsoft.Search) | Oui | [REST Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| SQL Data Warehouse | Oui | [Oui](https://portal.azure.com/#create/Microsoft.SQLDataWarehouse.0.1.12-preview) | | | |
| StorSimple | Non | Non | - | - | - | | Cache géré | Non | Non | - | - | - |

## Analyse

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Event Hub | Oui | Non | | [Créer un hub d’événements](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| Stream Analytics | Oui | [Oui](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) | | | |
| HDInsights | Oui | [Oui](https://portal.azure.com/#create/Microsoft.HDInsightCluster) | | | |
| Data Factory | Oui | [Oui](https://portal.azure.com/#create/Microsoft.DataFactory) | Oui | [Créer une fabrique de données](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| Machine Learning | Non | Non | - | - | - | | Catalogue de données | Non | Non | - | - | - |

## Mise en réseau

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Application Gateway | Oui | | | | |
| DNS | Oui | | | [Créer une zone DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Load Balancer | Oui | | | [Créer un équilibreur de charge](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Virtual Network | Oui | [Oui](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) | Non | [Créer un réseau virtuel](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Traffic Manager | Oui | Non | | [Créer un profil Traffic Manager](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| ExpressRoute | Oui | Non | Non | [ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |

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
| Sauvegarde | Non | Non | - | - | - | | Récupération de sites | Non | Non | - | - | - |

## Gestion des identités et des accès 

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | Non | Non | - | - | - | | Azure Actice Directory B2C | Non | Non | - | - | - | | Multi-Factor Authentication | Non | Non | - | - | - |

## Services de développement 

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | Oui | [Oui](https://portal.azure.com/#create/Microsoft.AppInsights.0.2.3-preview) | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Bing Maps | Oui | [Oui](https://portal.azure.com/#create/bingmaps.mapapis.1.0.4) | | | |
| Visual Studio Account | Oui | | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## Gestion 

| Service | Resource Manager activé | Portail en version préliminaire | Déplacer des ressources | API REST | Schéma |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Automation | Oui | [Oui](https://portal.azure.com/#create/Microsoft.AutomationAccount.1.0.2-preview) | | | |
| Key Vault | Oui | Non | Oui | [REST Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| Scheduler | Oui | Non | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Operational Insights | Oui | Non | Oui | | |
| IoTHubs | Oui | [Oui](https://portal.azure.com/#create/Microsoft.IotHub) | | | |


## Régions prises en charge

Quand vous déployez des ressources, vous devez généralement spécifier une région pour les ressources. Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource. Ces limitations peuvent être liées à la fiscalité appliquée dans votre pays de domiciliation, ou le résultat d’une stratégie mise en place par votre administrateur d’abonnement pour restreindre les régions disponibles.

Avant de déployer vos ressources, vérifiez quelles régions sont prises en charge pour votre type de ressource en exécutant l’une des commandes suivantes.

### API REST

Pour découvrir quelles régions sont disponibles pour un type de ressource particulier dans votre abonnement, utilisez l’opération [Liste de tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx).

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

Pour Azure PowerShell 0.9.8, utilisez la commande suivante :

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

### Interface de ligne de commande Azure

L’exemple suivant affiche la liste de tous les emplacements pris en charge pour chaque type de ressource.

    azure location list

Vous pouvez également filtrer les emplacements répertoriés à l’aide d’un outil tel que **jq**. Pour en savoir plus sur les outils tels que jq, consultez [Outils utiles pour interagir avec Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Résultat :

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## Versions d'API prises en charge

Lorsque vous déployez un modèle, vous devez spécifier une version de l'API à utiliser pour créer chaque ressource. La version de l'API correspond à une version des opérations de l'API REST publiées par le fournisseur de ressources. Lorsqu'un fournisseur de ressources active de nouvelles fonctionnalités, une nouvelle version de l'API REST sera publiée. Par conséquent, la version de l'API que vous spécifiez dans votre modèle affecte les propriétés que vous pouvez définir dans le modèle. En général, vous devez sélectionner la version de l'API la plus récente lorsque vous créez de nouveaux modèles. Pour les modèles existants, vous pouvez choisir soit de continuer d’utiliser une version de l'API antérieure, soit de mettre à jour votre modèle à la dernière version afin de tirer parti des nouvelles fonctionnalités.

### API REST

Pour découvrir quelles versions de l’API sont disponibles pour les types de ressources, utilisez l’opération [Liste de tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

L'exemple suivant montre comment obtenir les versions de l'API disponibles pour un type de ressource spécifique à l'aide d'Azure PowerShell 1.0 en version préliminaire.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
La sortie doit ressembler à ceci :
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Pour Azure PowerShell 0.9.8, utilisez :

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Interface de ligne de commande Azure

Vous pouvez enregistrer les informations (y compris les versions disponibles de l'API) pour un fournisseur de ressources dans un fichier avec la commande suivante.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Vous pouvez ouvrir le fichier et rechercher l’élément **apiVersions**.

## Étapes suivantes

- Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour en savoir plus sur le déploiement de ressources, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](./azure-portal/resource-group-template-deploy.md).

<!---HONumber=Nov15_HO1-->