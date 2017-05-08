---
title: Services pris en charge pour Resource Manager | Microsoft Docs
description: "Décrit les fournisseurs de ressources qui prennent en charge Resource Manager, ainsi que les schémas et versions d’API disponibles et les régions pouvant héberger les ressources."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: bf4e400e30cc93ca8fa0cc727ada0f1b224b05cb
ms.lasthandoff: 04/20/2017


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Fournisseurs, régions, schémas et versions d’API Resource Manager
Cette rubrique fournit une liste de fournisseurs de ressources qui prennent en charge Azure Resource Manager.

Quand vous déployez vos ressources, vous avez également besoin de connaître les régions qui prennent en charge ces ressources et quelles versions d'API sont disponibles pour les ressources. La section [Régions prises en charge](#supported-regions) vous explique comment trouver les régions dans lesquelles vous pourrez utiliser votre abonnement et vos ressources. La section [Versions d'API prises en charge](#supported-api-versions) vous explique comment déterminer les versions d'API que vous pouvez utiliser.

Pour voir quels services sont pris en charge dans le portail Azure et le portail Azure Classic, consultez [Tableau de la disponibilité des portails Azure](https://azure.microsoft.com/features/azure-portal/availability/). Pour connaître les services qui prennent en charge les ressources mobiles, consultez [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](resource-group-move-resources.md).

Les tableaux suivants indiquent si les services Microsoft répertoriés prennent en charge ou non le déploiement et la gestion via Resource Manager. Il existe également plusieurs fournisseurs de ressources de tiers qui prennent en charge le Resource Manager. Vous découvrirez comment voir tous les fournisseurs de ressources dans la section [Fournisseurs et types de ressources](#resource-providers-and-types).

## <a name="compute"></a>Calcul
| Service | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- |
| Batch |Oui |[Batch REST](/rest/api/batchservice) |[Ressources Batch](/azure/templates/microsoft.batch/batchaccounts) |
| Registre de conteneurs |Oui |[REST Registre de conteneurs](/rest/api/containerregistry) |[Ressources de Registre de conteneurs](/azure/templates/microsoft.containerregistry/registries) |
| Service de conteneur |Oui |[REST Service de conteneur](/rest/api/compute/containerservices) |[Ressources Container Service](/azure/templates/microsoft.containerservice/containerservices) |
| Dynamics Lifecycle Services |Oui | | |
| Jeux de mise à l’échelle |Oui |[REST Scale Sets](/rest/api/virtualmachinescalesets/) |[Ressources de groupe identique](/azure/templates/microsoft.compute/virtualmachinescalesets) |
| Service Fabric |Oui |[REST Service Fabric](/rest/api/servicefabric) | [Ressources Service Fabric](/azure/templates/microsoft.servicefabric/clusters) |
| Machines virtuelles |Oui |[REST Machine virtuelle](/rest/api/compute/virtualmachines) |[Ressources de machine virtuelle](/azure/templates/microsoft.compute/virtualmachines) |
| Machines virtuelles (classique) |Limité |- |- |
| Application distante |Non |- |- |
| Services cloud (classiques) |Limité (voir ci-dessous) |- |- |

Machines virtuelles (classiques) fait référence à des ressources qui ont été déployées via le modèle de déploiement classique, et non via le modèle de déploiement Resource Manager. En général, ces ressources ne prennent pas en charge les opérations de Resource Manager, sauf certaines opérations qui ont été activées. Pour plus d’informations sur ces modèles de déploiement, consultez [Présentation du déploiement Resource Manager et du déploiement classique](resource-manager-deployment-model.md). 

Les Services cloud (classiques) peuvent être utilisés avec d’autres ressources classiques. Toutefois, les ressources classiques ne tirent pas parti de toutes les fonctionnalités de Resource Manager et ne constituent pas une bonne option pour les futures solutions. Au lieu de cela, songez à modifier l’infrastructure de votre application pour qu’elle utilise les ressources des espaces de noms Microsoft.Compute, Microsoft.Storage et Microsoft.Network.

## <a name="networking"></a>Mise en réseau
| Service | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- |
| Application Gateway |Oui |[REST Application Gateway](https://msdn.microsoft.com/library/azure/mt684939.aspx) | [Ressources Application Gateway](/azure/templates/microsoft.network/applicationgateways) |
| DNS |Oui |[REST DNS](/rest/api/dns) |[Ressources DNS](/azure/templates/microsoft.network/dnszones) |
| ExpressRoute |Oui |[REST ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | [Ressources ExpressRoute](/azure/templates/microsoft.network/expressroutecircuits) |
| Load Balancer |Oui |[REST Load Balancer](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Ressources Load Balancer](/azure/templates/microsoft.network/loadbalancers) |
| Traffic Manager |Oui |[REST Traffic Manager](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Ressources Traffic Manager](/azure/templates/microsoft.network/trafficmanagerprofiles) |
| Virtual Network |Oui |[REST Réseau virtuel](https://msdn.microsoft.com/library/azure/mt163650.aspx) |[Ressources de réseau virtuel](/azure/templates/microsoft.network/virtualnetworks) |
| Passerelle réseau |Oui |[REST Passerelle réseau](https://msdn.microsoft.com/library/azure/mt163859.aspx) | [Ressources de connexion](/azure/templates/microsoft.network/connections) <br /> [Ressources de passerelle de réseau local](/azure/templates/microsoft.network/localnetworkgateways) <br /> [Ressources de passerelle de réseau virtuel](/azure/templates/microsoft.network/virtualnetworkgateways) |

## <a name="storage"></a>Storage
| de diffusion en continu | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- | --- |
| Importation - Exportation | Oui | [Importation et exportation REST](/rest/api/storageimportexport/) | [Importation et exportation de ressources](/azure/templates/microsoft.importexport/jobs) |
| Storage |Oui |[REST Stockage](/rest/api/storagerp) |[Ressources de stockage](/azure/templates/microsoft.storage/storageaccounts) |
| StorSimple |Oui | | |

## <a name="databases"></a>Bases de données
| Service | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- | --- |
| DocumentDB |Oui |[REST DocumentDB](/rest/api/documentdbresourceprovider) |[Ressources DocumentDB](/azure/templates/microsoft.documentdb/databaseaccounts) |
| Cache Redis |Oui | [REST du cache Redis](/rest/api/redis) |[Ressources Redis](/azure/templates/microsoft.cache/redis) |
| Base de données SQL |Oui |[REST SQL Database](/rest/api/sql) |[Ressources SQL Database](/azure/templates/microsoft.sql/servers) |
| SQL Data Warehouse |Oui | | |

## <a name="web--mobile"></a>Web et mobilité
| Service | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- |
| API Apps |Oui | [REST App Service](/rest/api/appservice) |[Ressources web](/azure/templates/microsoft.web/sites) |
| Gestion des API |Oui |[REST Gestion des API](/rest/api/apimanagement) |[Ressources Gestion des API](/azure/templates/microsoft.apimanagement/service) |
| Inscription de certificat | Oui | [Inscription de certificat REST](/rest/api/appservice/appservicecertificateorders) | [Ressources d’inscription de certificat](/azure/templates/microsoft.certificateregistration/certificateorders)  |
| Content Moderator |Oui | | | |
| Inscription de domaine | Oui | [Inscription de domaine](/rest/api/appservice/domains) | [Ressources d’inscription de domaine](/azure/templates/microsoft.domainregistration/domains)  |
| Function App |Oui | [REST Function App](/rest/api/appservice) | [Ressources web](/azure/templates/microsoft.web/sites) |
| Logic Apps |Oui |[REST Logic Apps](/rest/api/logic) |[Ressources d’application logique](/azure/templates/microsoft.logic/workflows) |
| Mobile Apps |Oui | [REST App Service](/rest/api/appservice) | [Ressources web](/azure/templates/microsoft.web/sites) |
| Mobile Engagements |Oui |[REST Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |
| Search |Oui |[REST Recherche](/rest/api/searchservice) | [Rechercher des ressources](/azure/templates/microsoft.search/searchservices) |
| Applications Web |Oui | [REST Web Apps](/rest/api/appservice/webapps) | [Ressources web](/azure/templates/microsoft.web/sites) |

## <a name="intelligence--analytics"></a>Intelligence et analyse
| Service | Resource Manager activé | API REST | Format de modèle | 
| --- | --- | --- | --- |
| Analysis Services | Oui | [REST Analysis Service](/rest/api/analysisservices) | [Ressources Analysis Services](/azure/templates/microsoft.analysisservices/servers) |
| Cognitive Services |Oui | [REST Cognitive Services](/rest/api/cognitiveservices) |[Ressources Cognitive Services](/azure/templates/microsoft.cognitiveservices/accounts) |
| Data Catalog |Oui |[Data Catalog REST](/rest/api/datacatalog) |[Schéma Data Catalog](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |
| Data Factory |Oui |[REST Data Factory](/rest/api/datafactory) | |
| Data Lake Analytics |Oui | [REST Data Lake](/rest/api/datalakeanalytics) |[Ressources Data Lake Analytics](/azure/templates/microsoft.datalakeanalytics/accounts) |
| Data Lake Store |Oui |[REST Data Lake Store](/rest/api/datalakestore) |[Ressources Data Lake Store](/azure/templates/microsoft.datalakestore/accounts) |
| HDInsights |Oui |[REST HDInsights](/rest/api/hdinsight) | |
| Machine Learning |Oui |[REST Machine Learning](/rest/api/machinelearning) |[Ressources Machine Learning](/azure/templates/microsoft.machinelearning/commitmentplans) |
| Stream Analytics |Oui |[Stream Analytics REST](/rest/api/streamanalytics) | |
| Power BI |Oui |[REST Power BI Embedded](/rest/api/powerbiembedded) |[Ressources Power BI](/azure/templates/microsoft.powerbi/workspacecollections) |


## <a name="internet-of-things"></a>Internet des Objets
| Service | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- |
| Event Hub |Oui |[REST Event Hub](/rest/api/eventhub) |[Ressources Event Hub](/azure/templates/microsoft.eventhub/namespaces) |
| IoTHubs |Oui |[REST IoT Hub](/rest/api/iothub) |[Ressources IoT Hub](/azure/templates/microsoft.devices/iothubs) |
| Notification Hubs |Oui |[REST Notification Hub](/rest/api/notificationhubs) |[Ressources Notification Hubs](/azure/templates/microsoft.notificationhubs/namespaces) |

## <a name="media--cdn"></a>Média et CDN
| Service | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- |
| CDN |Oui |[REST CDN](/rest/api/cdn) |[Ressources CDN](/azure/templates/microsoft.cdn/profiles) |
| Media Service |Oui |[REST Media Services](/rest/api/media) |[Ressources de données multimédias](/azure/templates/microsoft.media/mediaservices) |

## <a name="enterprise-integration"></a>Intégration d’entreprise
| Service | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- |
| BizTalk Services |Oui | |[Schéma BizTalk](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Relais | Oui |  | [Ressources Relay](/azure/templates/microsoft.relay/namespaces) |
| Service Bus |Oui |[REST Service Bus](/rest/api/servicebus) |[Ressources Service Bus](/azure/templates/microsoft.servicebus/namespaces) |

## <a name="identity--access-management"></a>Gestion des identités et des accès
Azure Active Directory fait appel au Gestionnaire de ressources pour prendre en charge le contrôle d’accès en fonction du rôle pour votre abonnement. Pour plus d’informations sur le contrôle d’accès en fonction du rôle et sur Azure Active Directory, consultez [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Services de développement
| Service | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- |
| Surveiller |Oui |[REST Monitor](/rest/api/monitor) |[Ressources Insights](/azure/templates/microsoft.insights/alertrules) |
| Bing Maps |Oui | | |
| DevTest Labs |Oui | [REST DevTest Labs](/rest/api/dtl) |[Ressources DevTest Labs](/azure/templates/microsoft.devtestlab/labs) |
| Visual Studio Account |Oui | |[Schéma Visual Studio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## <a name="management-and-security"></a>Gestion et sécurité
| Service | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- |
| Advisor | Oui | [Advisor REST](/rest/api/advisor/) | - |
| Automation |Oui |[REST Automation](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Ressources Automation](/azure/templates/microsoft.automation/automationaccounts) |
| Facturation | Oui | [Facturation REST](/rest/api/billing/) | - |
| Key Vault |Oui |[REST Key Vault](/rest/api/keyvault) |[Ressources Key Vault](/azure/templates/microsoft.keyvault/vaults) |
| Operational Insights |Oui | | |
| Recovery Services |Oui |[REST Recovery Services](/rest/api/recoveryservices) |[Ressources Recovery Services](/azure/templates/microsoft.recoveryservices/vaults) |
| Scheduler |Oui |[REST Scheduler](/rest/api/scheduler) |[Ressources Scheduler](/azure/templates/microsoft.scheduler/jobcollections) |
| Sécurité |Oui |[Security REST](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |
| Gestion de serveur | Oui | [Gestion de serveur REST](/rest/api/servermanagement/) | [Ressources de gestion de serveur](/azure/templates/microsoft.servermanagement/gateways) |

## <a name="resource-manager"></a>Gestionnaire de ressources
| Fonctionnalité | Resource Manager activé | API REST | Format de modèle |
| --- | --- | --- | --- | --- |
| Autorisation |Oui |[REST Autorisation](/rest/api/authorization) |[Ressources d’autorisation](/azure/templates/microsoft.authorization/locks) |
| les ressources |Oui |[REST Ressources](/rest/api/resources) |[Ressources de déploiement](/azure/templates/microsoft.resources/deployments) |

## <a name="resource-providers-and-types"></a>Fournisseurs et types de ressources
Lorsque vous déployez des ressources, vous devez fréquemment récupérer des informations sur les fournisseurs et les types de ressources. Vous pouvez récupérer ces informations via l’API REST, Azure PowerShell ou Azure CLI.

Pour fonctionner avec un fournisseur de ressources, ce fournisseur de ressources doit être enregistré avec votre compte. Par défaut, de nombreux fournisseurs de ressources sont enregistrés automatiquement ; toutefois, vous devrez peut-être inscrire manuellement certains fournisseurs de ressources. Les exemples dans cette section illustrent comment connaître l’état d’inscription d’un fournisseur de ressources et l’inscrire.

### <a name="portal"></a>Portail
Pour afficher la liste des fournisseurs de ressources pris en charge, il vous suffit de sélectionner **Fournisseurs de ressources** dans le panneau de l’abonnement. Pour inscrire votre abonnement auprès d’un fournisseur de ressources, sélectionnez le lien **Inscrire**.
   
![répertorier les fournisseurs de ressources](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>API REST
Pour obtenir tous les fournisseurs de ressources disponibles, notamment leur type, leur emplacement, les versions d’API et l’état de l’inscription, utilisez l’opération [Lister tous les fournisseurs de ressources](https://docs.microsoft.com/rest/api/resources/providers#Providers_List). Si vous devez inscrire un fournisseur de ressources, consultez [Register a subscription with a resource provider](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register)(Inscription d’un abonnement auprès d’un fournisseur de ressources).

### <a name="powershell"></a>PowerShell
L’exemple suivant montre comment obtenir tous les fournisseurs de ressources disponibles.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```


L’exemple suivant montre comment obtenir les types de ressources d’un fournisseur de ressources particulier.

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
```

Pour inscrire un fournisseur de ressources, fournissez l'espace de noms :

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement
```

### <a name="azure-cli"></a>Interface de ligne de commande Azure
L’exemple suivant montre comment obtenir tous les fournisseurs de ressources disponibles.

```azurecli
az provider list
```

Vous pouvez afficher les informations d’un fournisseur de ressources spécifique avec la commande suivante :

```azurecli
az provider show --namespace Microsoft.Web
```

Pour inscrire un fournisseur de ressources, fournissez l'espace de noms :

```azurecli
az provider register --namespace Microsoft.ServiceBus
```

## <a name="supported-regions"></a>Régions prises en charge
Quand vous déployez des ressources, vous devez généralement spécifier une région pour les ressources. Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource. Ces limitations peuvent être liées à la fiscalité appliquée dans votre pays de domiciliation, ou le résultat d’une stratégie mise en place par votre administrateur d’abonnement pour restreindre les régions disponibles. 

Pour obtenir la liste complète des régions prises en charge pour tous les services Azure, consultez la page [Services par région](https://azure.microsoft.com/regions/#services). Toutefois, cette liste peut inclure des régions que votre abonnement ne prend pas en charge. Vous pouvez déterminer les régions pour un type de ressource particulier pris en charge par votre abonnement via le portail, l’API REST, PowerShell ou l’interface de ligne de commande Azure.

### <a name="portal"></a>Portail
Vous pouvez voir les régions prises en charge pour un type de ressource avec les étapes suivantes :

1. Sélectionnez **Plus de services** > **Explorateur de ressources**.
   
    ![explorateur de ressources](./media/resource-manager-supported-services/select-resource-explorer.png)
2. Ouvrez le nœud **Fournisseurs**.
   
    ![sélectionnez les fournisseurs](./media/resource-manager-supported-services/select-providers.png)
3. Sélectionnez un fournisseur de ressources et affichez les régions et versions de l’API prises en charge.
   
    ![afficher le fournisseur](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>API REST
Pour découvrir quelles régions sont disponibles pour un type de ressource particulier dans votre abonnement, utilisez l’opération [Répertorier tous les fournisseurs de ressources](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
L’exemple suivant montre comment obtenir la liste des régions prises en charge pour les sites web.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

### <a name="azure-cli"></a>Interface de ligne de commande Azure
L’exemple suivant montre comment obtenir les emplacements pris en charge pour les sites web.

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```


## <a name="supported-api-versions"></a>Versions d'API prises en charge
Lorsque vous déployez un modèle, vous devez spécifier une version de l'API à utiliser pour créer chaque ressource. La version de l'API correspond à une version des opérations de l'API REST publiées par le fournisseur de ressources. Lorsqu'un fournisseur de ressources active de nouvelles fonctionnalités, une nouvelle version de l'API REST sera publiée. Par conséquent, la version de l'API que vous spécifiez dans votre modèle affecte les propriétés que vous pouvez définir dans le modèle. En général, vous devez sélectionner la version de l'API la plus récente lorsque vous créez des modèles. Pour les modèles existants, vous pouvez choisir soit de continuer d’utiliser une version de l'API antérieure, soit de mettre à jour votre modèle à la dernière version afin de tirer parti des nouvelles fonctionnalités.

### <a name="portal"></a>Portail
Vous déterminez les versions d’API prises en charge de la même façon que vous avez déterminé les régions prises en charge (expliqué précédemment).

### <a name="rest-api"></a>API REST
Pour découvrir quelles versions de l’API sont disponibles pour les types de ressource, utilisez l’opération [Répertorier tous les fournisseurs de ressources](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
L’exemple suivant montre comment obtenir les versions de l’API disponibles pour un type de ressource spécifique.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

Le résultat ressemble à ce qui suit :

```powershell
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
```

### <a name="azure-cli"></a>Interface de ligne de commande Azure
Vous obtenez les versions de l’API disponibles pour un fournisseur de ressources avec la commande suivante :

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour en savoir plus sur le déploiement de ressources, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).


