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
ms.date: 12/27/2016
ms.author: magoedte;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 53e57807e97671bd279c03ada4c147fc1e7f1e45
ms.openlocfilehash: c7bfc5584c11a7e69aedeb93f143a78d97c9369a


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Fournisseurs, régions, schémas et versions d’API Resource Manager
Azure Resource Manager vous offre un nouveau moyen de déployer et gérer les services qui composent vos applications. La plupart des services (mais pas tous) offrent une prise en charge complète ou partielle de Resource Manager. Cette rubrique fournit une liste de fournisseurs de ressources pris en charge pour Azure Resource Manager.

Quand vous déployez vos ressources, vous avez également besoin de connaître les régions qui prennent en charge ces ressources et quelles versions d'API sont disponibles pour les ressources. La section [Régions prises en charge](#supported-regions) vous explique comment trouver les régions dans lesquelles vous pourrez utiliser votre abonnement et vos ressources. La section [Versions d'API prises en charge](#supported-api-versions) vous explique comment déterminer les versions d'API que vous pouvez utiliser.

Pour voir quels services sont pris en charge dans le portail Azure et le portail Azure Classic, consultez [Tableau de la disponibilité des portails Azure](https://azure.microsoft.com/features/azure-portal/availability/). Pour connaître les services qui prennent en charge les ressources mobiles, consultez [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](resource-group-move-resources.md).

Les tableaux suivants indiquent si les services Microsoft répertoriés prennent en charge ou non le déploiement et la gestion via Resource Manager. Le lien de la colonne **Modèles de démarrage rapide** envoie une requête au référentiel de modèles de démarrage rapide Azure pour le fournisseur de ressources spécifié. Les modèles de démarrage rapide sont ajoutés et mis à jour fréquemment. La présence d’un lien pour un service particulier ne signifie pas nécessairement que la requête renvoie des modèles à partir du référentiel. Il existe également plusieurs fournisseurs de ressources de tiers qui prennent en charge le Resource Manager. Vous découvrirez comment voir tous les fournisseurs de ressources dans la section [Fournisseurs et types de ressources](#resource-providers-and-types).

## <a name="compute"></a>Calcul
| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- |
| Batch |Oui |[Batch REST](/rest/api/batchservice) |[Schéma Batch](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json) | |
| Container Registry |Oui |[Container Registry REST](/rest/api/containerregistry) |[Schéma Container Registry](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-27-preview/Microsoft.ContainerRegistry.json) |[Microsoft.ContainerRegistry](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerRegistry%22&type=Code) |
| Service de conteneur |Oui |[Service de conteneur REST](/rest/api/compute/containerservices) |[Schéma du conteneur](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) |[Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Dynamics Lifecycle Services |Oui | | | |
| Jeux de mise à l’échelle |Oui |[Jeu de mise à l’échelle REST](/rest/api/compute/virtualmachinescalesets) |[Schéma du groupe identique](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) |
| Service Fabric |Oui |[Service Fabric Rest](/rest/api/servicefabric) | [Schéma Service Fabric](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-09-01/Microsoft.ServiceFabric.json) |[Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Virtual Machines |Oui |[VM REST](/rest/api/compute/virtualmachines) |[Schéma de machine virtuelle](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Machines virtuelles (classique) |Limité |- |- |- |
| Application distante |Non |- |- |- |
| Services cloud (classiques) |Limité (voir ci-dessous) |- |- |- |

Machines virtuelles (classiques) fait référence à des ressources qui ont été déployées via le modèle de déploiement classique, et non via le modèle de déploiement Resource Manager. En général, ces ressources ne prennent pas en charge les opérations de Resource Manager, sauf certaines opérations qui ont été activées. Pour plus d’informations sur ces modèles de déploiement, consultez [Présentation du déploiement Resource Manager et du déploiement classique](resource-manager-deployment-model.md). 

Les Services cloud (classiques) peuvent être utilisés avec d’autres ressources classiques. Toutefois, les ressources classiques ne tirent pas parti de toutes les fonctionnalités de Resource Manager et ne constituent pas une bonne option pour les futures solutions. Au lieu de cela, songez à modifier l’infrastructure de votre application pour qu’elle utilise les ressources des espaces de noms Microsoft.Compute, Microsoft.Storage et Microsoft.Network.

## <a name="networking"></a>Mise en réseau
| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- |
| Application Gateway |Oui |[Passerelle d’application REST](https://msdn.microsoft.com/library/azure/mt684939.aspx) | |[applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS |Oui |[DNS REST](/rest/api/dns) |[Schéma DNS](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) |[dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute |Oui |[ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |[expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Load Balancer |Oui |[Équilibreur de charge REST](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Schéma de l’équilibreur de charge](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Traffic Manager |Oui |[Traffic Manager REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Schéma Traffic Manager](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) |[trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Virtual Network |Oui |[Réseau virtuel REST](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) |[Schéma du réseau virtuel](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Passerelle VPN |Oui |[Passerelle de réseau REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | |[virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |

## <a name="storage"></a>Storage
| de diffusion en continu | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- | --- |
| Storage |Oui |[Stockage REST](/rest/api/storagerp) |[Compte de stockage](resource-manager-template-storage.md) |[Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple |Oui | | | |

## <a name="databases"></a>Bases de données
| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- | --- |
| Base de données de documents |Oui |[REST DocumentDB](/rest/api/documentdbresourceprovider) |[Schéma DocumentDB](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) |[Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Cache Redis |Oui | [REST du cache Redis](/rest/api/redis) |[Schéma Redis](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) |[Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Base de données SQL |Oui |[Base de données SQL REST](/rest/api/sql) |[Schéma SQL Database](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |[Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL Data Warehouse |Oui | | | |

## <a name="web--mobile"></a>Web et mobilité
| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- |
| API Apps |Oui | [REST d’App Service](/rest/api/appservice) |[Schéma API Apps](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[API Apps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| API Management |Oui |[Gestion d’API REST](/rest/api/apimanagement) |[Schéma de Gestion des API](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json) |[Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) |
| Content Moderator |Oui | | | |
| Function App |Oui | [Function App REST](/rest/api/appservice) | |[functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Logic Apps |Oui |[Logic Apps REST](/rest/api/logic) |[Schéma Logic Apps](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) |[Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Mobile Apps |Oui | [REST d’App Service](/rest/api/appservice) |[Schéma Mobile Apps](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code) |
| Mobile Engagements |Oui |[REST Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |[Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Search |Oui |[REST Search](/rest/api/searchservice) | [Schéma de Recherche](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-19/Microsoft.Search.json) |[Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Applications Web |Oui | [Web Apps REST](/rest/api/appservice/webapps) |[Schéma Web Apps](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="intelligence--analytics"></a>Intelligence et analyse
| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- |
| Analysis Services | Oui | [Analysis Service REST](/rest/api/analysisservices) | [Schéma Analysis Services](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-16/Microsoft.AnalysisServices.json) | |
| Cognitive Services |Oui | [REST de Cognitive Services](/rest/api/cognitiveservices) |[Schéma Cognitive Services](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) | |
| Data Catalog |Oui |[Data Catalog REST](/rest/api/datacatalog) |[Schéma Data Catalog](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) | |
| Data Factory |Oui |[Data Factory REST](/rest/api/datafactory) | |[Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Data Lake Analytics |Oui | [REST du Data Lake](/rest/api/datalakeanalytics) |[Schéma Data Lake Analytics](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Data Lake Store |Oui |[Data Lake Store REST](/rest/api/datalakestore) |[Schéma Data Lake Store](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeStore.json) |[Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights |Oui |[HDInsights REST](/rest/api/hdinsight) | |[Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Apprentissage automatique |Oui |[Machine Learning REST](/rest/api/machinelearning) |[Schéma Machine Learning](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) | |
| Stream Analytics |Oui |[Stream Analytics REST](/rest/api/streamanalytics) | | |
| Power BI |Oui |[Power BI Embedded REST](/rest/api/powerbiembedded) |[Schéma Power BI](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) | |


## <a name="internet-of-things"></a>Internet des Objets
| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- |
| Hub d’événements |Oui |[Hub d’événements REST](/rest/api/eventhub) |[Schéma Event Hub](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) |[Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| IoTHubs |Oui |[IoT Hub REST](/rest/api/iothub) |[Schéma IoT Hub](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) |[Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Notification Hubs |Oui |[Notification Hub REST](/rest/api/notificationhubs) |[Schéma Notification Hub](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |[Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Média et CDN
| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- |
| CDN |Oui |[CDN REST](/rest/api/cdn) |[Schéma CDN](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) |[Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Media Service |Oui |[Media Services REST](/rest/api/media) |[Schéma Media](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |[Microsoft.Media](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Media%22&type=Code)  |

## <a name="hybrid-integration"></a>Intégration hybride
| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- |
| BizTalk Services |Oui | |[Schéma BizTalk](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | |
| Service de récupération |Oui |[Recovery Services REST](/rest/api/recoveryservices) |[Schéma Recovery Services](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) |[Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Service Bus |Oui |[REST Service Bus](/rest/api/servicebus) |[Schéma Service Bus](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json) |[Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Gestion des identités et des accès
Azure Active Directory fait appel au Gestionnaire de ressources pour prendre en charge le contrôle d’accès en fonction du rôle pour votre abonnement. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Services de développement
| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- |
| Surveiller |Oui |[Monitor REST](/rest/api/monitor) |[Schéma Insights](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |[Microsoft.Insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Maps |Oui | | | |
| DevTest Labs |Oui | [REST de DevTest](/rest/api/dtl) |[Schéma DevTest Lab](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) |[Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Visual Studio Account |Oui | |[Schéma Visual Studio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | |

## <a name="management-and-security"></a>Gestion et sécurité
| Service | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- |
| Automation |Oui |[Automation REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Schéma Automation](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json) |[Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Key Vault |Oui |[REST Key Vault](/rest/api/keyvault) |[Key vault](resource-manager-template-keyvault.md)<br />[Secret de coffre de clés](resource-manager-template-keyvault-secret.md) |[Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Operational Insights |Oui | | | |
| Scheduler |Oui |[Planificateur REST](/rest/api/scheduler) |[Schéma Scheduler](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) |[Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Sécurité (version préliminaire) |Oui |[Security REST](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |[Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## <a name="resource-manager"></a>Gestionnaire de ressources
| Fonctionnalité | Resource Manager activé | API REST | Schéma | Modèles de démarrage rapide |
| --- | --- | --- | --- | --- | --- |
| Autorisation |Oui |[Autorisation REST](/rest/api/authorization) |[Verrou de ressource](resource-manager-template-lock.md)<br />[Affectations de rôle](resource-manager-template-role.md) |[Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Ressources |Oui |[Ressources REST](/rest/api/resources) |[Liens de ressources](resource-manager-template-links.md) |[Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |

## <a name="resource-providers-and-types"></a>Fournisseurs et types de ressources
Lorsque vous déployez des ressources, vous devez fréquemment récupérer des informations sur les fournisseurs et les types de ressources. Vous pouvez récupérer ces informations via l’API REST, Azure PowerShell ou Azure CLI.

Pour fonctionner avec un fournisseur de ressources, ce fournisseur de ressources doit être enregistré avec votre compte. Par défaut, de nombreux fournisseurs de ressources sont enregistrés automatiquement ; toutefois, vous devrez peut-être inscrire manuellement certains fournisseurs de ressources. Les exemples ci-dessous illustrent comment connaître l’état d’inscription d’un fournisseur de ressources, et l’inscrire si nécessaire.

### <a name="portal"></a>Portail
Pour afficher la liste des fournisseurs de ressources pris en charge, il vous suffit de sélectionner **Fournisseurs de ressources** dans le panneau de l’abonnement. Pour inscrire votre abonnement auprès d’un fournisseur de ressources, sélectionnez le lien **Inscrire**.
   
![répertorier les fournisseurs de ressources](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>API REST
Pour obtenir tous les fournisseurs de ressources disponibles, notamment leur type, leur emplacement, les versions d’API et l’état de l’inscription, utilisez l’opération [Lister tous les fournisseurs de ressources](https://docs.microsoft.com/rest/api/resources/providers#Providers_List). Si vous devez inscrire un fournisseur de ressources, consultez [Register a subscription with a resource provider](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register)(Inscription d’un abonnement auprès d’un fournisseur de ressources).

### <a name="powershell"></a>PowerShell
L’exemple suivant montre comment obtenir tous les fournisseurs de ressources disponibles.

    Get-AzureRmResourceProvider -ListAvailable


L’exemple suivant montre comment obtenir les types de ressources d’un fournisseur de ressources particulier.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes

Le résultat ressemble à ce qui suit :

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...

Pour inscrire un fournisseur de ressources, fournissez l'espace de noms :

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>Interface de ligne de commande Azure
L’exemple suivant montre comment obtenir tous les fournisseurs de ressources disponibles.

    azure provider list

Le résultat ressemble à ce qui suit :

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Vous pouvez enregistrer les informations d’un fournisseur de ressources spécifique dans un fichier avec la commande suivante :

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Pour inscrire un fournisseur de ressources, fournissez l'espace de noms :

    azure provider register -n Microsoft.ServiceBus

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

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Le résultat ressemble à ce qui suit :

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

### <a name="azure-cli"></a>Interface de ligne de commande Azure
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

## <a name="supported-api-versions"></a>Versions d'API prises en charge
Lorsque vous déployez un modèle, vous devez spécifier une version de l'API à utiliser pour créer chaque ressource. La version de l'API correspond à une version des opérations de l'API REST publiées par le fournisseur de ressources. Lorsqu'un fournisseur de ressources active de nouvelles fonctionnalités, une nouvelle version de l'API REST sera publiée. Par conséquent, la version de l'API que vous spécifiez dans votre modèle affecte les propriétés que vous pouvez définir dans le modèle. En général, vous devez sélectionner la version de l'API la plus récente lorsque vous créez des modèles. Pour les modèles existants, vous pouvez choisir soit de continuer d’utiliser une version de l'API antérieure, soit de mettre à jour votre modèle à la dernière version afin de tirer parti des nouvelles fonctionnalités.

### <a name="portal"></a>Portail
Vous déterminez les versions d’API prises en charge de la même façon que vous avez déterminé les régions prises en charge (expliqué précédemment).

### <a name="rest-api"></a>API REST
Pour découvrir quelles versions de l’API sont disponibles pour les types de ressource, utilisez l’opération [Répertorier tous les fournisseurs de ressources](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
L’exemple suivant montre comment obtenir les versions de l’API disponibles pour un type de ressource spécifique.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

Le résultat ressemble à ce qui suit :

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

### <a name="azure-cli"></a>Interface de ligne de commande Azure
Vous pouvez enregistrer les informations (y compris les versions disponibles de l’API) d’un fournisseur de ressources dans un fichier avec la commande suivante :

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Vous pouvez ouvrir le fichier et rechercher l’élément **apiVersions** .

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour en savoir plus sur le déploiement de ressources, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).




<!--HONumber=Feb17_HO2-->


