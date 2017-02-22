---
title: "Guide du développeur Azure Government | Microsoft Docs"
description: "Cet article dresse une analyse comparative des fonctionnalités et fournit des conseils pour le développement d’applications pour Azure Government."
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: 0bab116e977a8490bf0c114c8b829aa1ce07e923
ms.openlocfilehash: 41af18426ce3f8124458a61c3af280c3dd17cfcd


---
# <a name="azure-government-developer-guide"></a>Guide du développeur Azure Government
L’environnement Azure Government est une instance physique distincte du reste du réseau de Microsoft. Ce guide présente les différences que les développeurs d’applications et les administrateurs doivent comprendre pour utiliser des régions distinctes d’Azure.

## <a name="overview"></a>Vue d’ensemble
Azure Government est une instance distincte du service Microsoft Azure. Il répond aux besoins de sécurité et de conformité des agences fédérales des États-Unis, des autorités d’État et locales, mais aussi à ceux de leurs fournisseurs de solutions. Azure Government offre un isolement physique et réseau par rapport aux déploiements qui ne sont pas ceux du gouvernement américain. Par ailleurs, il met à disposition un personnel américain sélectionné.

Microsoft fournit un certain nombre d'outils pour aider les développeurs à créer et déployer des applications cloud pour le service global Microsoft Azure (« service global ») et les services Microsoft Azure Government.

Lorsque des développeurs créent et déploient des applications pour les services Azure Government, par opposition au service global, ils doivent connaître les principales différences entre ces deux services. Ils doivent notamment bien comprendre l’installation et la configuration de leur environnement de programmation, la configuration des points de terminaison, l’écriture des applications et leur déploiement en tant que services dans Azure Government.

Les informations contenues dans ce document résument les différences entre les deux services. Elles complètent les informations disponibles sur le site [Azure Government](http://www.azure.com/gov "Azure Government") et sur le site de la [bibliothèque technique de Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") sur MSDN. Des informations officielles sont également disponibles à de nombreux autres emplacements, par exemple le [Centre de gestion de la confidentialité Microsoft Azure](https://azure.microsoft.com/support/trust-center/ "Centre de gestion de la confidentialité Microsoft Azure"), le [Centre de documentation Azure](https://azure.microsoft.com/documentation/) et les [blogs Azure](https://azure.microsoft.com/blog/ "blogs Azure").

Ce contenu est destiné aux partenaires et développeurs qui effectuent des déploiements dans Microsoft Azure Government.

## <a name="guidance-for-developers"></a>Aide pour les développeurs
La plupart du contenu technique disponible actuellement part du principe que les applications sont développées pour le service global plutôt que pour Azure Government. C’est pourquoi il est important de connaître les deux principales différences concernant les applications que vous développez pour l’hébergement dans Azure Government.

* Il est possible que certains services et fonctionnalités liés à des régions spécifiques du service global ne soient pas disponibles dans Azure Government.
* La configuration des fonctionnalités dans Azure Government peut différer de celle du service global. Ainsi, il est important de vérifier vos exemples de code, configurations et étapes de travail pour vous assurer que vous générez et exécutez des applications dans l'environnement des services cloud Azure Government.

Actuellement, les centres de données prenant en charge Azure Government sont ceux du Gouvernement des États-Unis - Iowa et du Gouvernement des États-Unis - Virginie. Pour connaître les centres de données et les services actuellement disponibles, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services).


## <a name="endpoint-mapping"></a>Mappage de point de terminaison
Pour en savoir plus sur le mappage des points de terminaison publics Microsoft Azure et SQL Database à des points de terminaison Azure Government spécifiques, consultez le tableau suivant :

| Nom | Point de terminaison Azure Government |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId  | https://management.core.usgovcloudapi.net/ |
| GalleryUrl | https://gallery.usgovcloudapi.net/ |
| ManagementPortalUrl | https://manage.windowsazure.us |
| ServiceManagementUrl | https://management.core.usgovcloudapi.net/ |
| PublishSettingsFileUrl | https://manage.windowsazure.us/publishsettings/index |
| ResourceManagerUrl | https://management.usgovcloudapi.net/ |
| SqlDatabaseDnsSuffix | .database.usgovcloudapi.net |
| StorageEndpointSuffix | core.usgovcloudapi.net |
| ActiveDirectoryAuthority | https://login-us.microsoftonline.com/ |
| GraphUrl | https://graph.windows.net/ |
| GraphEndpointResourceId | https://graph.windows.net/ |
| TrafficManagerDnsSuffix | usgovtrafficmanager.net |
| AzureKeyVaultDnsSuffix | vault.usgovcloudapi.net |
| AzureKeyVaultServiceEndpointResourceId | https://vault.usgovcloudapi.net |



## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur Azure Government, consultez les ressources suivantes :

* [S’inscrire pour un essai](https://azuregov.microsoft.com/trial/azuregovtrial)
* [Achat et utilisation d’Azure Government](http://azure.com/gov)
* [Vue d’ensemble d’Azure Government](/azure-government-overview)
* [Blog d’Azure Government](http://blogs.msdn.microsoft.com/azuregov/)
* [Conformité d’Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)



<!--HONumber=Feb17_HO3-->


