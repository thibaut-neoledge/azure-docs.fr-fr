<properties
   pageTitle="Services, régions, schémas et versions pris en charge par Resource Manager | Microsoft Azure"
   description="Décrit les fournisseurs de ressources qui prennent en charge Resource Manager, ainsi que les schémas et versions d’API disponibles et les régions pouvant héberger les ressources."
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
   ms.date="12/22/2015"
   ms.author="tomfitz"/>

# Fournisseurs, régions, schémas et versions d’API Resource Manager

Azure Resource Manager vous offre un nouveau moyen de déployer et gérer les services qui composent vos applications. La plupart des services (mais pas tous) offrent une prise en charge complète ou partielle de Resource Manager. Microsoft prévoit d’activer Resource Manager pour les principaux services dans les solutions futures. En attendant que ce processus soit terminé, il est important pour vous de connaître l’état actuel de la prise en charge pour chaque service. Cette rubrique répertorie les fournisseurs de ressources pris en charge pour Azure Resource Manager.

Quand vous déployez vos ressources, vous avez également besoin de connaître les régions qui prennent en charge ces ressources et quelles versions d'API sont disponibles pour les ressources. La section [Régions prises en charge](#supported-regions) vous explique comment trouver les régions dans lesquelles vous pourrez utiliser votre abonnement et vos ressources. La section [Versions d'API prises en charge](#supported-api-versions) vous explique comment déterminer les versions d'API que vous pouvez utiliser.

Pour voir quels services sont pris en charge dans le portail Azure et le portail Azure Classic, consultez [Tableau de la disponibilité des portails Azure](https://azure.microsoft.com/features/azure-portal/availability/).

Les tableaux suivants indiquent si les services répertoriés prennent en charge ou non le déploiement et la gestion via Resource Manager. La colonne intitulée **Déplacer des ressources** indique si les ressources du type en question peuvent être déplacées vers un nouveau groupe de ressources et un nouvel abonnement. Le lien de la colonne **Modèles de démarrage rapide** envoie une requête au référentiel de modèles de démarrage rapide Azure pour le fournisseur de ressources spécifié. Les modèles de démarrage rapide sont ajoutés et mis à jour fréquemment. La présence d’un lien pour un service particulier ne signifie pas nécessairement que la requête renvoie des modèles à partir du référentiel.


## Calcul

| Service | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------------------------ | -------------- |-------- | ------ | ------ |
| Virtual Machines | Oui | Non | [Créer une machine virtuelle](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [Microsoft.Compute](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%22&type=Code) |
| Batch | Oui | Oui | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | | [Microsoft.Batch](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Batch%22&type=Code) |
| Dynamics Lifecycle Services | Oui | | | | [Microsoft.DynamicsLcs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DynamicsLcs%22&type=Code)
| Service Fabric (version préliminaire) | Oui | | [Service Fabric Rest](https://msdn.microsoft.com/library/azure/dn707692.aspx) | | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Machines virtuelles (classiques) | Limité | Partiel (voir ci-dessous) | - | - |
| Remote App | Non | - | - | - |

Machines virtuelles (classiques) fait référence à des ressources qui ont été déployées via le modèle de déploiement classique, et non via le modèle de déploiement Resource Manager. En général, ces ressources ne prennent pas en charge les opérations de Resource Manager, sauf certaines opérations qui ont été activées. Pour plus d’informations sur ces modèles de déploiement, consultez [Présentation du déploiement Resource Manager et du déploiement classique](resource-manager-deployment-model.md).

Les ressources Machines virtuelles (classiques) peuvent être déplacées vers un nouveau groupe de ressources, mais pas vers un nouvel abonnement.

## Mise en réseau

| Service | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Application Gateway | Oui | | | | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS | Oui | | [Créer une zone DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| Load Balancer | Oui | | [Créer un équilibreur de charge](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Virtual Network | Oui | Non | [Créer un réseau virtuel](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Traffic Manager | Oui | | [Créer un profil Traffic Manager](https://msdn.microsoft.com/library/azure/mt163581.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| ExpressRoute | Oui | Non | [ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |

## Données et stockage

| Service | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------------- | -------- | ------ | ------- | ------ |
| Base de données de documents | Oui | Oui | [REST DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Storage | Oui | Non | [Créer un stockage](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [Compte de stockage](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| Cache Redis | Oui | Oui | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Base de données SQL | Oui | Oui | [Créer une base de données](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Search | Oui | Oui | [REST Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) | | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| SQL Data Warehouse | Oui | | | |
| StorSimple | Non | - | - | - | | Cache géré | Non | - | - | - |

## Web et mobilité

| Service | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| API Management | Oui | Oui | [Créer une API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| API Apps | Oui | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) | [API Apps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Applications Web | Oui | Oui, avec des limitations (voir ci-dessous) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |
| Notification Hubs | Oui | Oui | [Créer un hub de notification](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |
| Logic Apps | Oui | Oui | | | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Mobile Engagements | Oui | Oui | | | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |

Lorsque vous travaillez avec des applications web, vous ne pouvez pas déplacer uniquement un plan App Service. Pour déplacer des applications web, les options disponibles sont :

- Déplacer toutes les ressources d'un groupe de ressources à un autre, si le groupe de ressources de destination n'a pas déjà de ressources Microsoft.Web.
- Déplacer les applications web vers un autre groupe de ressources, mais conserver le plan App Service dans le groupe de ressources d'origine.

## Analyse

| Service | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Hub d’événements | Oui | | [Créer un hub d’événements](https://msdn.microsoft.com/library/azure/dn790676.aspx) | | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| Stream Analytics | Oui | | [Stream Analytics REST](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | [Microsoft.StreamAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.StreamAnalytics%22&type=Code) |
| HDInsights | Oui | Oui | [Créer un cluster](https://msdn.microsoft.com/library/azure/mt622302.aspx) | | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Data Factory | Oui | Oui | [Créer une fabrique de données](https://msdn.microsoft.com/library/azure/dn906717.aspx) | | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Machine Learning | Non | - | - | - | | Data Catalog | Non | - | - | - |

## Média et CDN

| Service | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| CDN | Oui | | [CDN REST](https://msdn.microsoft.com/library/azure/mt634456.aspx) | | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Media Service | Non | | | |


## Intégration hybride

| Service | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| BizTalk Services | Oui | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | [Microsoft.BizTalkServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BizTalkServices%22&type=Code) |
| Service Bus | Oui | | [REST Service Bus](https://msdn.microsoft.com/library/azure/hh780717.aspx) | | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |
| Backup | Non | - | - | - | | Site Recovery | Non | - | - | - |

## Gestion des identités et des accès 

| Service | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Azure Active Directory | Non | - | - | - | | Azure Actice Directory B2C | Non | - | - | - | | Multi-Factor Authentication | Non | - | - | - |

## Services de développement 

| Service | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Application Insights | Oui | Non | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.Insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Maps | Oui | | | | [Microsoft.BingMaps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BingMaps%22&type=Code) |
| Dev/Test Labs (version préliminaire) | Oui | | | [2015-05-21-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-05-21-preview/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Visual Studio Account | Oui | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | [Microsoft.VisualStudio](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.VisualStudio%22&type=Code) |

## Gestion et sécurité

| de diffusion en continu | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Automation | Oui | Oui | | | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Key Vault | Oui | Oui | [REST Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Coffre de clés](resource-manager-template-keyvault.md)<br />[Secret de coffre de clés](resource-manager-template-keyvault-secret.md) | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Scheduler | Oui | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Operational Insights | Oui | Oui | | | [Microsoft.OperationalInsights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.OperationalInsights%22&type=Code) |
| IoTHubs | Oui | | [Créer un IoT Hub](https://msdn.microsoft.com/library/azure/mt589013.aspx) | | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Sécurité (version préliminaire) | Oui | | | | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## Gestionnaire de ressources

| Fonctionnalité | Resource Manager activé | Déplacer des ressources | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | -------------- | -------- | ------ | ------ |
| Autorisation | Oui | N/A | [Verrous de gestion](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Contrôle d’accès en fonction du rôle](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Verrou de ressource](resource-manager-template-lock.md)<br />[Affectations de rôle](resource-manager-template-role.md) | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Ressources | Oui | N/A | [Ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Liens de ressources](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## Régions prises en charge

Quand vous déployez des ressources, vous devez généralement spécifier une région pour les ressources. Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource. Ces limitations peuvent être liées à la fiscalité appliquée dans votre pays de domiciliation, ou le résultat d’une stratégie mise en place par votre administrateur d’abonnement pour restreindre les régions disponibles.

Pour obtenir la liste complète de toutes les régions prises en charge pour l’ensemble des services Azure, consultez [Services par région](https://azure.microsoft.com/regions/#services). Toutefois, cette liste peut inclure des régions que votre abonnement ne prend pas en charge. Vous pouvez déterminer les régions pour un type de ressource spécifique que votre abonnement prend en charge en exécutant l'une des commandes suivantes.

### API REST

Pour découvrir quelles régions sont disponibles pour un type de ressource particulier dans votre abonnement, utilisez l’opération [Répertorier tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

L’exemple suivant montre comment obtenir la liste des régions prises en charge pour les sites web utilisant Azure PowerShell 1.0. Pour plus d’informations sur Azure PowerShell 1.0, consultez [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

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

Pour découvrir quelles versions de l’API sont disponibles pour les types de ressource, utilisez l’opération [Répertorier tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

L’exemple suivant montre comment obtenir les versions de l’API disponibles pour un type de ressource spécifique à l’aide d’Azure PowerShell 1.0.

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
- Pour en savoir plus sur le déploiement de ressources, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_1223_2015-->