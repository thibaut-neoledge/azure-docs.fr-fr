<properties
   pageTitle="Services pris en charge pour Resource Manager | Microsoft Azure"
   description="Décrit les fournisseurs de ressources qui prennent en charge Resource Manager, ainsi que les schémas et versions d’API disponibles et les régions pouvant héberger les ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/26/2016"
   ms.author="magoedte;tomfitz"/>

# Fournisseurs, régions, schémas et versions d’API Resource Manager

Azure Resource Manager vous offre un nouveau moyen de déployer et gérer les services qui composent vos applications. La plupart des services (mais pas tous) offrent une prise en charge complète ou partielle de Resource Manager. Microsoft prévoit d’activer Resource Manager pour les principaux services dans les solutions futures. En attendant que ce processus soit terminé, il est important pour vous de connaître l’état actuel de la prise en charge pour chaque service. Cette rubrique répertorie les fournisseurs de ressources pris en charge pour Azure Resource Manager.

Quand vous déployez vos ressources, vous avez également besoin de connaître les régions qui prennent en charge ces ressources et quelles versions d'API sont disponibles pour les ressources. La section [Régions prises en charge](#supported-regions) vous explique comment trouver les régions dans lesquelles vous pourrez utiliser votre abonnement et vos ressources. La section [Versions d'API prises en charge](#supported-api-versions) vous explique comment déterminer les versions d'API que vous pouvez utiliser.

Pour voir quels services sont pris en charge dans le portail Azure et le portail Azure Classic, consultez [Tableau de la disponibilité des portails Azure](https://azure.microsoft.com/features/azure-portal/availability/). Pour connaître les services qui prennent en charge les ressources mobiles, consultez [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](resource-group-move-resources.md).

Les tableaux suivants indiquent si les services répertoriés prennent en charge ou non le déploiement et la gestion via Resource Manager. Le lien de la colonne **Modèles de démarrage rapide** envoie une requête au référentiel de modèles de démarrage rapide Azure pour le fournisseur de ressources spécifié. Les modèles de démarrage rapide sont ajoutés et mis à jour fréquemment. La présence d’un lien pour un service particulier ne signifie pas nécessairement que la requête renvoie des modèles à partir du référentiel.


## Calcul

| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------------------------ |-------- | ------ | ------ |
| Batch | Oui | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | | [Microsoft.Batch](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Batch%22&type=Code) |
| Dynamics Lifecycle Services | Oui | | | [Microsoft.DynamicsLcs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DynamicsLcs%22&type=Code)
| Service Fabric (version préliminaire) | Oui | [Service Fabric Rest](https://msdn.microsoft.com/library/azure/dn707692.aspx) | | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Virtual Machines | Oui | [VM REST](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [Microsoft.Compute](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%22&type=Code) |
| Machines virtuelles (classique) | Limité | - | - | 
| Application distante | Non | - | - | 
| Cloud Services (classique) | Limité (voir ci-dessous) | - | - | - |

Machines virtuelles (classiques) fait référence à des ressources qui ont été déployées via le modèle de déploiement classique, et non via le modèle de déploiement Resource Manager. En général, ces ressources ne prennent pas en charge les opérations de Resource Manager, sauf certaines opérations qui ont été activées. Pour plus d’informations sur ces modèles de déploiement, consultez [Présentation du déploiement Resource Manager et du déploiement classique](resource-manager-deployment-model.md).

Cloud Services (classique) peut être utilisé avec d’autres ressources classiques. Toutefois, les ressources classiques ne tirent pas parti de toutes les fonctionnalités du Gestionnaire de ressources et ne constituent pas une bonne option pour les futures solutions. Au lieu de cela, songez à modifier l’infrastructure de votre application pour qu’elle utilise les ressources des espaces de noms Microsoft.Compute, Microsoft.Storage et Microsoft.Network.


## Mise en réseau

| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | -------  | -------- | ------ | ------ |
| Application Gateway | Oui | | | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS | Oui | [DNS REST](https://msdn.microsoft.com/library/azure/mt163862.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute | Oui | [ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Load Balancer | Oui | [Équilibreur de charge REST](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Traffic Manager | Oui | [Traffic Manager REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Virtual Network | Oui| [Réseau virtuel REST](https://msdn.microsoft.com/fr-FR/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Passerelle VPN | Oui | [Passerelle de réseau REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[connexions](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |



## Données et stockage

| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------- | ------ |
| Base de données de documents | Oui | [REST DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Cache Redis | Oui | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Search | Oui | [REST Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) | | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Storage | Oui | [Stockage REST](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Compte de stockage](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| Base de données SQL | Oui | [Base de données SQL REST](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL Data Warehouse | Oui | | |
| StorSimple | Non | - | - | - | - |

## Web et mobilité

| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| API Apps | Oui | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) | [API Apps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| API Management | Oui | [Gestion d’API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) | | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Logic Apps | Oui | | | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Mobile Apps | Oui | | | |
| Mobile Engagements | Oui | | | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Applications Web | Oui | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## Analyse

| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | -------  | -------- | ------ | ------ |
| Data Factory | Oui | [Data Factory REST](https://msdn.microsoft.com/library/azure/dn906738.aspx) | | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Data Lake Analytics | Oui | | | |
| Data Lake Store | Oui | | | |
| HDInsights | Oui | [HDInsights REST](https://msdn.microsoft.com/library/azure/mt622197.aspx) | | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Stream Analytics | Oui | [Stream Analytics REST](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | [Microsoft.StreamAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.StreamAnalytics%22&type=Code) |
| Machine Learning | Non | - | - |
| Catalogue de données | Non | - | - |

## Internet des Objets

| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Hub d’événements | Oui | [Hub d’événements REST](https://msdn.microsoft.com/library/azure/dn790674.aspx) | | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| IoTHubs | Oui | [IoT Hub REST](https://msdn.microsoft.com/library/azure/mt589014.aspx) | | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Notification Hubs | Oui | [Notification Hub REST](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## Média et CDN

| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| CDN | Oui | [CDN REST](https://msdn.microsoft.com/library/azure/mt634456.aspx) | | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Media Service | Non | | |


## Intégration hybride

| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| BizTalk Services | Oui | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | [Microsoft.BizTalkServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BizTalkServices%22&type=Code) |
| Service Bus | Oui | | | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## Gestion des identités et des accès 

Azure Active Directory fait appel au Gestionnaire de ressources pour prendre en charge le contrôle d’accès en fonction du rôle pour votre abonnement. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

## Services de développement 

| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Application Insights | Oui | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.Insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Maps | Oui | | | [Microsoft.BingMaps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BingMaps%22&type=Code) |
| Dev/Test Labs (version préliminaire) | Oui | | [2015-05-21-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-05-21-preview/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Visual Studio Account | Oui | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | [Microsoft.VisualStudio](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.VisualStudio%22&type=Code) |

## Gestion et sécurité

| de diffusion en continu | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------- | ------ | ------ |
| Automation | Oui | [Automation REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) | | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Key Vault | Oui | [REST Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Coffre de clés](resource-manager-template-keyvault.md)<br />[Secret de coffre de clés](resource-manager-template-keyvault-secret.md) | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Operational Insights | Oui | | | [Microsoft.OperationalInsights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.OperationalInsights%22&type=Code) |
| Recovery Services | Oui | | | |
| Scheduler | Oui | [Planificateur REST](https://msdn.microsoft.com/library/azure/mt629143.aspx) | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Sécurité (version préliminaire) | Oui | | | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## Gestionnaire de ressources

| Fonctionnalité | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Autorisation | Oui | [Verrous de gestion](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Contrôle d’accès en fonction du rôle](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Verrou de ressource](resource-manager-template-lock.md)<br />[Affectations de rôle](resource-manager-template-role.md) | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Ressources | Oui | [Ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Liens de ressources](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## Fournisseurs et types de ressources

Lorsque vous déployez des ressources, vous devez fréquemment récupérer des informations sur les fournisseurs et les types de ressources. Vous pouvez récupérer ces informations via l’API REST, Azure PowerShell ou Azure CLI.

Pour fonctionner avec un fournisseur de ressources, ce fournisseur de ressources doit être enregistré avec votre compte. Par défaut, de nombreux fournisseurs de ressources sont enregistrés automatiquement ; toutefois, vous devrez peut-être inscrire manuellement certains fournisseurs de ressources. Les exemples ci-dessous illustrent comment connaître l’état d’inscription d’un fournisseur de ressources, et l’inscrire si nécessaire.

### API REST

Pour obtenir tous les fournisseurs de ressources disponibles, notamment leur type, leur emplacement, les versions d’API et l’état de l’inscription, utilisez l’opération [Répertorier tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx). Si vous devez inscrire un fournisseur de ressources, consultez [Register a subscription with a resource provider](https://msdn.microsoft.com/library/azure/dn790548.aspx) (Inscription d’un abonnement auprès d’un fournisseur de ressources).

### PowerShell

L’exemple suivant montre comment obtenir tous les fournisseurs de ressources disponibles.

    Get-AzureRmResourceProvider -ListAvailable
    
La sortie doit ressembler à ceci :

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    ...

L’exemple suivant montre comment obtenir les types de ressources d’un fournisseur de ressources particulier.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
La sortie doit ressembler à ceci :

    ResourceTypeName                Locations                                         ApiVersions
    ----------------                ---------                                         ------
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} {20...
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} {20...
    ...
    
Pour inscrire un fournisseur de ressources, fournissez l'espace de noms :

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### Interface de ligne de commande Azure

L’exemple suivant montre comment obtenir tous les fournisseurs de ressources disponibles.

    azure provider list
    
La sortie doit ressembler à ceci :

    info:    Executing command provider list
    + Getting ARM registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Vous pouvez enregistrer les informations pour un fournisseur de ressources spécifique dans un fichier avec la commande suivante.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Pour inscrire un fournisseur de ressources, fournissez l'espace de noms :

    azure provider register -n Microsoft.ServiceBus

## Régions prises en charge

Quand vous déployez des ressources, vous devez généralement spécifier une région pour les ressources. Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource. Ces limitations peuvent être liées à la fiscalité appliquée dans votre pays de domiciliation, ou le résultat d’une stratégie mise en place par votre administrateur d’abonnement pour restreindre les régions disponibles.

Pour obtenir la liste complète de toutes les régions prises en charge pour l’ensemble des services Azure, consultez [Services par région](https://azure.microsoft.com/regions/#services). Toutefois, cette liste peut inclure des régions que votre abonnement ne prend pas en charge. Vous pouvez déterminer les régions pour un type de ressource spécifique que votre abonnement prend en charge en exécutant l'une des commandes suivantes.

### API REST

Pour découvrir quelles régions sont disponibles pour un type de ressource particulier dans votre abonnement, utilisez l’opération [Répertorier tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

L’exemple suivant montre comment obtenir la liste des régions prises en charge pour les sites web.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
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

Vous pouvez également filtrer les emplacements répertoriés à l’aide d’un utilitaire JSON tel que [jq](https://stedolan.github.io/jq/).

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

L’exemple suivant montre comment obtenir les versions de l’API disponibles avantages un type de ressource spécifique.

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

### Interface de ligne de commande Azure

Vous pouvez enregistrer les informations (y compris les versions disponibles de l'API) pour un fournisseur de ressources dans un fichier avec la commande suivante.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Vous pouvez ouvrir le fichier et rechercher l’élément **apiVersions**.

## Étapes suivantes

- Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour en savoir plus sur le déploiement de ressources, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0601_2016-->