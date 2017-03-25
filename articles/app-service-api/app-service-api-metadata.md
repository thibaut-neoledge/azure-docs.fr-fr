---
title: "Métadonnées d’App Service API Apps pour la détection d’API et la création de code | Microsoft Docs"
description: "Découvrez comment les applications API dans Azure App Service utilisent des métadonnées Swagger pour faciliter la détection d’API et la génération de code."
services: app-service\api
documentationcenter: .net
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: c7f8e33a-61cc-486f-89df-4a97dc3c71d4
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 92057ef0c40bfc79a66265d2d369d7abf72adc8e
ms.lasthandoff: 03/21/2017


---
# <a name="app-service-api-apps-metadata-for-api-discovery-and-code-generation"></a>Les métadonnées d’App Service API Apps pour la détection d’API et la création de code
La prise en charge des métadonnées d’API [Swagger 2.0](http://swagger.io/) est intégrée aux applications API App Service. Vous n’êtes pas obligé d’utiliser Swagger, mais si vous le faites, vous pouvez tirer parti des fonctionnalités d’applications API qui facilitent la détection et la consommation.   

## <a name="swagger-endpoint"></a>Point de terminaison Swagger
Vous pouvez spécifier un point de terminaison qui fournit les métadonnées de JSON Swagger 2.0 pour une application API dans une propriété de l’application API. Le point de terminaison peut être associé à l’URL de base de l’application API ou une URL absolue. Les URL absolues peuvent pointer hors de l’application API. 

Pour de nombreux clients en aval (par exemple, génération de code Visual Studio et flux d’ajout d’API de PowerApps), l’URL doit être accessible publiquement (c’est-à-dire non protégée par une authentification de l’utilisateur ou du service). Autrement dit, si vous utilisez l’authentification App Service et que vous souhaitez exposer la définition d’API depuis votre application, vous devez utiliser l’option d’authentification qui permet au trafic anonyme d’atteindre votre API. Pour plus d’informations, consultez la page [Authentification et autorisation pour les applications d’API dans Azure App Service](app-service-api-authentication.md).

### <a name="portal-blade"></a>Panneau du portail
Dans le [portail Azure](https://portal.azure.com/) , l’URL du point de terminaison peut être vue et modifiée sur le panneau **Définition d’API** .

![](./media/app-service-api-metadata/apidefblade.png)

### <a name="azure-resource-manager-property"></a>Propriété d’Azure Resource Manager
Vous pouvez également configurer l’URL de définition d’API pour une application API à l’aide de [l’Explorateur de ressources](https://resources.azure.com/) ou des [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) dans les outils en ligne de commande, par exemple [Azure PowerShell](/powershell/azureps-cmdlets-docs) et [l’interface de ligne de commande Azure](../cli-install-nodejs.md). 

Dans **l’Explorateur de ressources**, accédez à **abonnements > {votre abonnement} > resourceGroups > {votre groupe de ressources} > fournisseurs > Microsoft.Web > sites > {votre site} > config > web**. Vous verrez la propriété `apiDefinition` :

        "apiDefinition": {
          "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
        }

Pour obtenir un exemple de modèle Azure Resource Manager qui définit la propriété `apiDefinition` , ouvrez le fichier [azuredeploy.json dans l’exemple d’application de liste des tâches](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Recherchez la section du modèle qui ressemble à l’exemple JSON ci-dessus.

### <a name="default-value"></a>Valeur par défaut
Lorsque vous utilisez Visual Studio pour créer une application API, le point de terminaison de définition d’API est automatiquement défini sur l’URL de base de l’application API plus `/swagger/docs/v1`. Il s’agit de l’URL par défaut que le package [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet utilise pour distribuer des métadonnées Swagger générées de façon dynamique pour un projet d’API Web ASP.NET. 

## <a name="code-generation"></a>Génération de code
Un des avantages de l’intégration de Swagger dans Azure API apps est la génération de code automatique. Les classes de client générées facilitent l’écriture du code qui appelle une application API.

Vous pouvez générer du code client pour une application API en utilisant Visual Studio ou à partir de la ligne de commande. Pour plus d’informations sur la génération des classes client dans Visual Studio pour un projet d’API Web ASP.NET, consultez [Prise en main des applications API et d’ASP.NET](app-service-api-dotnet-get-started.md#codegen). Pour plus d’informations sur la façon de le faire à partir de la ligne de commande pour toutes les langues prises en charge, consultez le fichier Lisezmoi du référentiel [Azure/autorest](https://github.com/azure/autorest) dans GitHub.com.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir un didacticiel qui vous guide dans la création, le déploiement et l’utilisation d’une application API, consultez [Prise en main des applications API dans Azure App Service](app-service-api-dotnet-get-started.md).

Si vous utilisez Gestion des API Azure avec API Apps, vous pouvez utiliser les métadonnées Swagger pour importer votre API dans Gestion des API. Pour plus d’informations, consultez [Importation de la définition d’une API avec des opérations dans Gestion des API Azure](../api-management/api-management-howto-import-api.md). 


