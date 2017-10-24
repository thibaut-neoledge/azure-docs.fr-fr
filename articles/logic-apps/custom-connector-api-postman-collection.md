---
title: "Décrire les API et connecteurs personnalisés avec Postman - Azure Logic Apps | Microsoft Docs"
description: "Créer des collections Postman pour décrire, regrouper et organiser vos API et connecteurs personnalisés"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>Décrire les API personnalisées et les connecteurs personnalisés avec Postman

Pour faciliter et accélérer le développement [d’API personnalisées](../logic-apps/logic-apps-create-api-app.md) et de [connecteurs personnalisés](../logic-apps/custom-connector-overview.md), vous pouvez créer des collections [Postman](https://www.getpostman.com/), plutôt que des documents OpenAPI, pour décrire vos API et connecteurs. Les collections Postman vous aident à organiser et à regrouper les requêtes d’API connexes. Par exemple, vous pouvez utiliser Postman lors de la création de connecteurs pour Azure Logic Apps, Microsoft Flow ou Microsoft PowerApps. 

Ce didacticiel montre comment créer une [collection Postman](https://www.getpostman.com/docs/postman/collections/creating_collections) à l’aide de [l’API Détecter la langue](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) dans [l’Analyse de texte Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/text-analytics/) en tant qu’exemple. Cette API identifie la langue, le sentiment et les phrases clés dans le texte que vous transmettez à l’API.

## <a name="prerequisites"></a>Composants requis

* Si vous ne connaissez pas Postman, [installez l’application Postman](https://www.getpostman.com/apps).

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez commencer par créer [gratuitement un compte Azure](https://azure.microsoft.com/free/). Sinon, vous pouvez souscrire à un [abonnement de type paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/).

* Si vous avez un abonnement Azure, inscrivez-vous aux API Analyse de texte en effectuant la [Tâche 1 ici](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md). 

## <a name="create-a-postman-collection"></a>Créer une collection Postman

Avant de pouvoir créer une collection, créez une requête HTTP pour le point de terminaison de votre API. 

### <a name="create-an-http-request-for-your-api"></a>Créer une requête HTTP pour votre API

1. Ouvrez l’application Postman pour créer une [requête HTTP](https://www.getpostman.com/docs/postman/sending_api_requests/requests) pour le point de terminaison de votre API. Pour plus d’informations, consultez la [documentation sur les requêtes](https://www.getpostman.com/docs/postman/sending_api_requests/requests) de Postman.

   1. Dans l’onglet **Builder**, sélectionnez la méthode HTTP, entrez l’URL de la requête pour le point de terminaison de votre API et sélectionnez un protocole d’autorisation, le cas échéant. 
   Une fois ces opérations effectuées, sélectionnez **Params**.

      Pour ce didacticiel, vous pouvez utiliser ces paramètres dans cet exemple :

      ![Créer une requête : « Méthode HTTP », « URL de requête », « Autorisation »](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | Paramètre | Valeur suggérée | 
      | --------- | --------------- | 
      | **Méthode HTTP** | POST | 
      | **URL de requête** | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **Autorisation** | « No Auth » | | 
      ||| 

   2. Vous pouvez désormais entrer des paires clé-valeur à utiliser en tant que paramètres de requête ou de chemin d’accès dans l’URL de requête. Postman combine ces éléments en une chaîne de requête.
   Une fois ces opérations effectuées, sélectionnez **Headers**.

      ![Suite de la requête : « Parameters »](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | Paramètre | Valeur suggérée | 
      | --------- | --------------- | 
      | **Params** | **Clé** : « numberOfLanguagesToDetect » </br>**Valeur** : « 1 » | 
      ||| 

   3. Entrez les paires clé-valeur pour l’en-tête de la requête. 
   Pour le nom d’en-tête, entrez la chaîne que vous voulez. Pour les en-têtes HTTP communs, vous pouvez faire votre choix dans la liste déroulante. Une fois ces opérations effectuées, sélectionnez **Body**. 
   
      ![Suite de la requête : « Headers »](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | Paramètre | Valeur | 
      | --------- | ----- | 
      | **En-têtes** | **Clé** : « Ocp-Apim-Subscription-Key » </br>**Valeur** : *clé-d’abonnement-de-votre-API*, que vous trouverez dans votre compte Cognitive Services <p>**Clé** : « Content-Type » </br> **Valeur** : « application/json » | 
      ||| 

   4. Entrez le contenu que vous souhaitez envoyer dans le corps de la requête. 
   Pour vérifier que la requête fonctionne et obtient une réponse, choisissez **Send**. 
   
      ![Suite de la requête : « Body »](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | Paramètre | Valeur suggérée | 
      | --------- | --------------- |    
      | **Corps** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      Le champ de la réponse contient la réponse complète de l’API, notamment le résultat ou erreur, le cas échéant.

      ![Obtenir une réponse à la requête](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. Après avoir vérifié que votre requête fonctionne, enregistrez-la dans une collection Postman. 

   1. Choisissez **Enregistrer**. 
      
      ![Choisir « Enregistrer »](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. Sous **Enregistrer la requête**, entrez un **Nom de la requête** et, éventuellement, une **Description de la requête**. 

      > [!NOTE]
      > Votre connecteur personnalisé utilise ces valeurs ultérieurement. Par conséquent, assurez-vous de fournir les mêmes valeurs que celles utilisées ultérieurement pour la description et le résumé d’opération de votre API personnalisée.

   3. Choisissez **+ Créer une collection** et entrez un nom pour la collection. 
   Choisissez la coche, ce qui crée un dossier de collection, puis choisissez **Enregistrer sous *nom-de-votre-collection-Postman***.

      ![Enregistrer une requête](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>Enregistrer la réponse à la requête

Une fois que vous avez enregistré votre requête, vous pouvez enregistrer la réponse. De cette façon, la réponse s’affiche comme exemple lorsque vous chargez la demande ultérieurement.

1. Au-dessus de la fenêtre de la réponse, choisissez **Enregistrer la réponse**. 

   ![Enregistrer la réponse](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   Les connecteurs personnalisés ne prennent en charge qu’une seule réponse par requête. 
   Si vous enregistrez plusieurs réponses par requête, seule la première est utilisée.

2. Entrez un nom pour votre exemple, puis choisissez **Enregistrer l’exemple**.

3. Poursuivez la génération de votre collection Postman avec d’autres requêtes et d’autres réponses.

### <a name="export-your-postman-collection"></a>Exporter votre collection Postman

1. Lorsque vous avez terminé, exportez votre collection vers un fichier JSON.

   ![Exporter la collection](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. Choisissez le format d’exportation **Collection v1**, puis accédez à l’emplacement où vous souhaitez enregistrer le fichier JSON.

   > [!NOTE]
   > Actuellement, seul le format V1 fonctionne pour les connecteurs personnalisés.

   ![Choisir le format d’exportation : « Collection v1 »](./media/custom-connector-api-postman-collection/09-export-format.png)
   
Vous pouvez désormais utiliser cette collection Postman pour créer des connecteurs et des API personnalisés. Après avoir exporté la collection, vous pouvez importer le fichier JSON dans Logic Apps, Flow ou PowerApps.

> [!IMPORTANT]
> Si vous créez un connecteur personnalisé à partir d’une collection Postman, assurez-vous de supprimer l’en-tête `Content-type` des actions et des déclencheurs. Le service cible, par exemple Flow, ajoute automatiquement cet en-tête. En outre, supprimez les en-têtes d’authentification dans la section `securityDefintions` des actions et des déclencheurs.

## <a name="next-steps"></a>Étapes suivantes

* [Logic Apps : Inscrire des connecteurs personnalisés](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow : Inscrire votre connecteur](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps : Inscrire votre connecteur](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [FAQ sur le connecteur personnalisé](../logic-apps/custom-connector-faq.md)
