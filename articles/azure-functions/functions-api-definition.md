---
title: "Métadonnées OpenAPI dans les fonctions Azure Functions | Microsoft Docs"
description: "Vue d’ensemble de la prise en charge d’OpenAPI dans les fonctions Azure"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: aac362f5123038cc39e0d2b32c10f7747a702cfe
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Prise en charge des métadonnées OpenAPI 2.0 dans les fonctions Azure (version préliminaire)
Cette fonctionnalité de la version préliminaire vous permet d’écrire une définition OpenAPI 2.0 (anciennement Swagger) dans Function App et d’héberger ce fichier à l’aide de l’application Function.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-metadata"></a>Que sont les métadonnées OpenAPI ?
[Métadonnées OpenAPI](http://swagger.io/) permet à une grande variété d’autres logiciels de consommer une fonction hébergeant une API REST. Notamment, des offres propriétaires, telles que PowerApps et [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), ou des outils de développeurs tiers, tels que [Postman](https://www.getpostman.com/docs/importing_swagger), et [de nombreux autres packages](http://swagger.io/tools/).

>[!TIP]
>Nous vous conseillons de commencer par le [didacticiel de mise en route](./functions-api-definition-getting-started.md) , puis de revenir à ce document pour en savoir plus sur les fonctionnalités spécifiques.

## <a name="enable"></a>Activation la prise en charge de la définition OpenAPI
* Tous les paramètres OpenAPI peuvent être configurés sur la page `API Definition (preview)` des paramètres de votre application Function.
* Paramétrez `API definition source` sur `Function` pour activer une définition OpenAPI hébergée et une génération de définition à démarrage rapide.
  * `External URL` permet à votre fonction d’utiliser une définition OpenAPI qui est hébergée ailleurs.

## <a name="generate-definition"></a>Générer une structure Swagger à partir des métadonnées de votre fonction
Le modèle est la solution idéale pour commencer à écrire votre première définition OpenAPI. La fonctionnalité de modèle de définition crée une définition OpenAPI partielle à l’aide de toutes les métadonnées dans function.json, pour chacune de vos fonctions de déclencheur HTTP. **Vous devez renseigner plus d’informations sur votre API à partir de la [spécification OpenAPI](http://swagger.io/specification/), comme les modèles de requête et de réponse.**

[Consultez ce didacticiel de prise en main pour obtenir la procédure détaillée.](./functions-api-definition-getting-started.md)

### <a name="templates"></a>Modèles disponibles

|Nom| Description |
|:-----|:-----|
|Définition générée|Une définition OpenAPI avec la quantité maximale d’informations pouvant être déduites à partir de métadonnées existantes de la fonction|

### <a name="quickstart-details"></a>Métadonnées incluses dans la définition générée

La table suivante représente les paramètres du portail et les données correspondantes dans function.json telle qu’elles sont mappées sur la structure Swagger générée.

|Swagger.json|Interface utilisateur du portail|Function.json|
|:----|:-----|:-----|
|[Hôte](http://swagger.io/specification/#fixed-fields-15)|`Function app settings` > `Go to App Service Settings` > `Overview` > `URL`|*non présent*
|[Chemins d’accès](http://swagger.io/specification/#paths-object-29)|`Integrate` > `Selected HTTP methods`|Liaisons : itinéraire
|[Élément du chemin d’accès](http://swagger.io/specification/#path-item-object-32)|`Integrate` > `Route template`|Liaisons : méthodes
|[Sécurité](http://swagger.io/specification/#security-scheme-object-112)|de clés symétriques|*non présent*|
|operationID*|Itinéraire + verbes autorisés|Itinéraire + verbes autorisés|

\*L’ID de l’opération est nécessaire uniquement pour l’intégration avec PowerApps + Flow
> [!NOTE]
>  x-ms-summary fournit un nom d’affichage dans Logic Apps, Flow et PowerApps.
>
> Consultez la section [Personnaliser votre définition Swagger pour PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) pour en savoir plus.

## <a name="CICD"></a>Définir une définition d’API avec CI/CD

 Vous devez activer l’hébergement de la définition de l’API dans le portail avant d’activer le contrôle de code source pour modifier votre définition d’API à partir du contrôle de code source. Procédez comme suit.

1. Accédez à `API Definition (preview)` dans les paramètres de votre application Function.
  1. Paramétrez `API definition source` sur `Function`
  1. Cliquez sur `Generate API definition template` puis `Save` pour créer une définition de modèle à modifier ultérieurement.
  1. Notez votre `API definition URL` et votre `key`
1. [Configurer CI/CD](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements)
2. Modifier votre `swagger.json` dans le contrôle de code source sur `\site\wwwroot\.azurefunctions\swagger\swagger.json`

Désormais, les modifications apportées à `swagger.json` dans votre référentiel sont hébergées par votre application Function, sur `API definition URL` et `key` notées à l’étape 1.3

## <a name="next-steps"></a>Étapes suivantes
* [Didacticiel de prise en main](functions-api-definition-getting-started.md)
  * Essayez notre procédure détaillée pour voir une définition OpenAPI en action !
* [Référentiel GitHub Azure Functions](https://github.com/Azure/Azure-Functions/)
  * Consultez le Github Functions pour nous donner votre avis sur la version préliminaire du support de définition d’API ! Créez un ticket github pour les mises à jour que vous souhaitez.
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)  
  * Référence du programmeur pour le codage de fonctions et la définition de déclencheurs et de liaisons.

