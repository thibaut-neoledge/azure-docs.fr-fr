---
title: "Prise en main des métadonnées OpenAPI dans Azure Functions | Microsoft Docs"
description: "Prise en main de la prise en charge d’OpenAPI dans Azure Functions"
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
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 9fd5b63259c7771d8b1e346ba3a6c10a4399532a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/01/2017


---
# <a name="creating-openapi-20-swagger-metadata-for-a-function-app-preview"></a>Création de métadonnées OpenAPI 2.0 (Swagger) pour une Function App (version préliminaire)

Ce document vous guide pas à pas dans la création d’une définition OpenAPI pour une API simple hébergée sur Azure Functions.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-swagger"></a>Qu’est-ce qu’OpenAPI (Swagger) ?
[Métadonnées Swagger](http://swagger.io/) est un fichier qui définit les fonctionnalités et les modes de fonctionnement de votre API et permet à une fonction qui héberge une API REST d’être utilisée par une grande variété d’autres logiciels. Les offres Microsoft telles que PowerApps et [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), ainsi que des outils de développeurs tiers tels que [Postman](https://www.getpostman.com/docs/importing_swagger) et [de nombreux autres packages](http://swagger.io/tools/) permettent à votre API d’être utilisée avec une définition Swagger.

## <a name="prepare-function"></a>Création d’une fonction avec une API simple
  Pour créer une définition OpenAPI, nous devons tout d’abord créer une fonction avec une API simple. Si vous disposez déjà d’une API hébergée sur une Function App, vous pouvez passer directement à la section suivante
1. Créez une nouvelle Function App.
  1. [Portail Azure](https://portal.azure.com) > `+ New` > Recherchez « Function App »
1. Créer une nouvelle fonction de déclencheur HTTP à l’intérieur de votre nouvelle Function App
  1. Votre fonction est préremplie avec du code définissant une API REST très simple.
  1. N’importe quelle chaîne transmise à la fonction en tant que paramètre de requête ou dans le corps est retournée en tant que « Bonjour {entrée} »
1. Accédez à l’onglet `Integrate` de votre nouvelle fonction de déclencheur HTTP
  1. Basculer `Allowed HTTP methods` sur `Selected methods`
  1. Dans `Selected HTTP methods`, désactivez chaque verbe sauf POST.
    ![Méthodes HTTP sélectionnées](./media/functions-api-definition-getting-started/selectedHTTPmethods.png)
  1. Cette étape simplifiera votre définition d’API.

## <a name="enable"></a>Activation de la prise en charge de la définition d’API
1. Accédez à `your function name` > `API Definition (preview)`
1. Paramétrez `API Definition Source` sur `Function`
  1. Cette étape active une suite d’options OpenAPI pour votre Function App, notamment un point de terminaison pour héberger un fichier OpenAPI à partir du domaine de votre Function App, une copie en ligne de l’[éditeur OpenAPI](http://editor.swagger.io) et un générateur de définition de démarrage rapide.
![Définition activée](./media/functions-api-definition-getting-started/enabledefinition.png)

## <a name="create-definition"></a>Création de votre définition d’API à partir d’un modèle
1. Cliquez sur `Generate API Definition template`
  1. Cette étape analyse votre Function App pour trouver des fonctions de déclencheur HTTP et utilise les informations contenues dans functions.json pour générer un document OpenAPI.
2. Ajouter un objet d’opération à `paths: /api/yourfunctionroute post:`
  1. Le document OpenAPI de démarrage rapide est un plan d’un document OpenAPI complet. Elle nécessite davantage de métadonnées pour être une définition OpenAPI complète, telles que des objets d’opération et des modèles de réponse.
  1. L’exemple d’objet d’opération ci-dessous dispose d’une section production/consommation remplie, d’un objet de paramètre et d’un objet de réponse.
  
  ```yaml
      post:
        operationId: /api/yourfunctionroute/post
        consumes: [application/json]
        produces: [application/json]
        parameters:
          - name: name
            in: body
            description: Your name
            x-ms-summary: Your name
            required: true
            schema:
              type: object
              properties:
                name:
                  type: string
        description: >-
          Replace with Operation Object
          #http://swagger.io/specification/#operationObject
        responses:
          '200':
            description: A Greeting
            x-ms-summary: Your name
            schema:
              type: string
        security:
          - apikeyQuery: []
  ```

> [!NOTE]
>  x-ms-summary fournit un nom d’affichage dans Logic Apps, Flow et PowerApps.
>
> Consultez la section [Personnaliser votre définition Swagger pour PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) pour en savoir plus.

3. Cliquez sur `save` pour enregistrer vos modifications ![Ajout d’une définition de modèle](./media/functions-api-definition-getting-started/addingtemplate.png)

## <a name="use-definition"></a>Utilisation de votre définition d’API
1. Copiez-collez votre `API definition URL` dans un nouvel onglet de navigateur pour afficher votre document OpenAPI brut.
1. Vous pouvez importer votre document OpenAPI dans un nombre illimité d’outils de test et l’intégration via cette URL.
  1. De nombreuses ressources Azure peuvent importer automatiquement votre document OpenAPI à l’aide de l’URL de définition d’API qui est enregistrée dans vos paramètres d’application de fonction. Dans le cadre de la source de définition d’API `Function`, nous mettons à jour cette URL pour vous.


## <a name="test-definition"></a>Utilisation de l’interface utilisateur Swagger pour tester votre définition d’API
Des outils de test sont intégrés à l’affichage interface utilisateur de l’éditeur de définition d’API intégré. Ajoutez votre clé d’API, puis utilisez le bouton `Try this operation` sous chaque méthode. L’outil utilisera votre définition d’API pour mettre en forme les demandes et les réponses de réussite indiqueront que la définition est correcte.

### <a name="steps"></a>Étapes :

1. Copier votre clé d’API de fonction
  1. Vous trouverez la clé d’API dans votre fonction de déclencheur HTTP sous `function name` > `Keys` > `Function Keys`
  ![Clé de fonction](./media/functions-api-definition-getting-started/functionkey.png)
1. Accédez à la page `API Definition (preview)`.
  1. Cliquez sur `Authenticate` et ajoutez votre clé d’API de fonction à l’objet de sécurité en haut.
  ![Clé OpenAPI](./media/functions-api-definition-getting-started/definitionTest auth.png)
1. sélectionnez `/api/yourfunctionroute` > `POST`
1. Cliquez sur `Try it out` et entrez un nom pour le test
1. Vous devriez voir un résultat RÉUSSITE sous `Pretty`
![Test de définition d’API](./media/functions-api-definition-getting-started/definitionTest.png)

## <a name="next-steps"></a>Étapes suivantes
* [Définition OpenAPI dans Vue d’ensemble de Functions](functions-api-definition.md)
  * Lisez la documentation complète pour plus d’informations sur la prise en charge d’OpenAPI.
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)  
  * Référence du programmeur pour le codage de fonctions et la définition de déclencheurs et de liaisons.
* [Référentiel GitHub Azure Functions](https://github.com/Azure/Azure-Functions/)
  * Consultez le GitHub Functions pour nous donner votre avis sur la version préliminaire du support de définition d’API ! Créez un ticket GitHub pour les mises à jour que vous souhaitez.

