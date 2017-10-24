---
title: "Vue d’ensemble des connecteurs personnalisés - Azure Logic Apps | Microsoft Docs"
description: "Vue d’ensemble de la création de connecteurs personnalisés pour prendre en charge et étendre les scénarios d’intégration."
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
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>Vue d’ensemble des connecteurs personnalisés

Sans écrire de code, vous pouvez créer des workflows ou des applications avec [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), [Microsoft Flow](https://flow.microsoft.com) ou [Microsoft PowerApps](https://powerapps.microsoft.com). Pour vous aider à intégrer vos données et vos processus d’entreprise, ces services offrent [plus de 100 connecteurs](../connectors/apis-list.md), non seulement pour les produits et services Microsoft comme Azure et SQL Server, mais aussi pour d’autres services, notamment GitHub, Salesforce, Twitter et bien plus encore. 

Cependant, il se peut que vous souhaitiez dans certains cas appeler des API, des services et des systèmes qui ne sont pas disponibles en tant que connecteurs créés au préalable. Pour prendre en charge des scénarios plus adaptés aux exigences de productivité et aux besoins professionnels de vos utilisateurs, vous pouvez créer des *connecteurs personnalisés* avec leurs propres déclencheurs et leurs propres actions.
Les connecteurs personnalisés étendent les intégrations, la portée, la découvrabilité et l’utilisation de votre service ou produit, ce qui peut contribuer à augmenter et à accélérer l’adoption par les clients.

Par exemple, ce diagramme illustre l’interaction entre une API Web, un connecteur Logic Apps personnalisé créé pour cette API et une application logique qui fonctionne avec cette API à l’aide du connecteur personnalisé :

![Vue d’ensemble conceptuelle d’une API Web, d’un connecteur personnalisé et d’une application logique](./media/custom-connector-overview/custom-connector-conceptual.png)

Cette vue d’ensemble présente dans les grandes lignes les tâches générales de création, de sécurisation, d’inscription et d’utilisation, ainsi que de partage et de certification, de vos connecteurs :

![Étapes de création d’un connecteur personnalisé](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>Composants requis

Pour créer votre connecteur de A à Z, vous aurez besoin de ces éléments :

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez commencer avec un [compte Azure gratuit](https://azure.microsoft.com/free/). Sinon, souscrivez à un [abonnement avec paiement au fur et à mesure](https://azure.microsoft.com/pricing/purchase-options/).

* Une API RESTful avec un certain type d’accès authentifié. Pour plus d’informations, consultez la page [Créer des connecteurs personnalisés à partir d’API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Vous trouverez des modèles que vous pourrez utiliser pour les déclencheurs et les actions de votre connecteur sur la page [Créer des API personnalisées qui seront appelées par des workflows d’applications logiques](../logic-apps/logic-apps-create-api-app.md).

* L’un de ces éléments :

  * un [fichier OpenAPI 2.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md), précédemment appelé Swagger ;
  * l’URL d’une définition OpenAPI ;
  * une [collection Postman](../logic-apps/custom-connector-api-postman-collection.md) pour votre API. 

  Si vous n’avez aucun de ces éléments, nous vous aiderons à les obtenir.

* Facultatif : une image à utiliser comme icône pour votre connecteur personnalisé.

## <a name="1-build-your-restful-api"></a>1. Créer une API RESTful

Techniquement, un connecteur est un wrapper autour d’une API REST basée sur une spécification OpenAPI (anciennement Swagger) et permettant au service sous-jacent de communiquer avec Logic Apps, Flow ou PowerApps. Tout d’abord, il vous faut une API entièrement fonctionnelle pour pouvoir créer votre connecteur personnalisé. 

Vous pouvez utiliser le langage et la plateforme de votre choix pour votre API. En ce qui concerne les technologies Microsoft, nous recommandons ces plateformes :

* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure Web Apps](https://azure.microsoft.com/services/app-service/web/)
* [Azure API Apps](https://azure.microsoft.com/services/app-service/api/)

Par exemple, ce didacticiel explique [comment créer un connecteur personnalisé à partir d’une API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Vous trouverez des modèles que vous pourrez utiliser pour les déclencheurs et les actions de votre connecteur sur la page [Créer des API personnalisées qui seront appelées par des workflows d’applications logiques](../logic-apps/logic-apps-create-api-app.md).

## <a name="2-secure-your-api"></a>2. Sécuriser l’API

Vous pouvez utiliser ces standards d’authentification pour les connecteurs et les API :

   * [OAuth 2.0](https://oauth.net/2/), y compris [Azure Active Directory](https://azure.microsoft.com/develop/identity/) ou des services spécifiques, notamment Dropbox, GitHub et Salesforce
   * Generic OAuth 2.0
   * [Authentification de base](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [Clé API](https://swagger.io/docs/specification/authentication/api-keys/)

Vous pouvez configurer l’authentification Azure Active Directory (Azure AD) pour votre API sur le Portail Azure, de façon à ne pas avoir à implémenter l’authentification avec du code. Vous pouvez également exiger et appliquer une authentification par le biais du code de votre API. 

Pour plus d’informations, suivez les didacticiels correspondants :

* [Logic Apps : Sécuriser un connecteur personnalisé](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Flow : Sécuriser un connecteur personnalisé](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [PowerApps : Sécuriser un connecteur personnalisé](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3. Décrire l’API 

À supposer que votre API ait un certain type d’accès authentifié, vous devez décrire son interface et ses opérations afin que Logic Apps, Flow ou PowerApps puisse communiquer avec elle.
Utilisez l’une de ces définitions standard du marché :

* Un [fichier OpenAPI 2.0](https://swagger.io/). Vous pouvez commencer la génération avec un fichier OpenAPI existant.

  Si vous ne connaissez pas encore OpenAPI, consultez la page [Prise en main de Swagger](http://swagger.io/getting-started/) sur le site swagger.io.
  Vous trouverez un exemple de fichier OpenAPI dans la [Documentation de l’API Analyse de texte](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure). 

* Une collection Postman, ce qui génère automatiquement un fichier OpenAPI. Si vous n’avez pas encore de fichier OpenAPI et que vous ne souhaitez pas en créer, vous pouvez quand même créer un connecteur personnalisé sans difficulté, à l’aide d’une collection Postman.

  Si vous ne connaissez pas Postman, [installez l’application Postman](https://www.getpostman.com/apps) sur leur site. Pour plus d’informations, consultez la page [Décrire des connecteurs personnalisés avec Postman](../logic-apps/custom-connector-api-postman-collection.md).

> [!IMPORTANT]
> La taille de votre fichier doit être inférieure à 1 Mo.

En arrière-plan, Logic Apps, Flow et PowerApps utilisent en définitive OpenAPI, analysent une collection Postman et la traduisent en un fichier de définition OpenAPI. Même si les collections OpenAPI 2.0 et Postman utilisent des formats différents, ce sont dans les deux cas des documents lisibles par machine et indépendants du langage qui décrivent les opérations et les paramètres de l’API. Vous pouvez générer ces documents avec différents outils, en fonction du langage et de la plateforme utilisés par votre API. Vous pouvez également créer un fichier OpenAPI au moment où vous inscrivez votre connecteur.

Par exemple, vous pouvez créer un fichier OpenAPI ou une collection Postman à partir de n’importe quel point de terminaison d’API REST, notamment :

* des connecteurs disponibles publiquement, par exemple, [Spotify](https://developer.spotify.com/), [Slack](https://api.slack.com/), [Rackspace](http://docs.rackspace.com/), etc. ;

* une API créée et déployée sur n’importe quel fournisseur d’hébergement cloud, par exemple, Azure, Heroku, Google Cloud, etc. ;

* une API métier personnalisée déployée sur votre réseau, mais seulement si elle est exposée sur l’Internet public.

## <a name="4-register-your-connector"></a>4. Inscrire le connecteur

Le processus d’inscription aide Logic Apps, Flow ou PowerApps à comprendre les caractéristiques de l’API, à savoir la description, l’authentification requise et les opérations, y compris les paramètres et les sorties de chaque opération. Lorsque vous démarrez le processus d’inscription, vous pouvez fournir un fichier OpenAPI ou bien une collection Postman, qui remplit automatiquement les champs de métadonnées dans l’Assistant Inscription. Vous pouvez modifier les valeurs de ces champs à tout moment.

![Décrire l’API](./media/custom-connector-overview/choose-api-definition-file.png)

Pour inscrire votre connecteur, suivez le didacticiel correspondant :

* [Logic Apps : Inscrire un connecteur](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow : Inscrire un connecteur](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps : Inscrire un connecteur](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5. Utiliser le connecteur dans une application logique, un flux ou une application 

Vous pouvez utiliser votre connecteur de la même façon que des connecteurs gérés par Microsoft. Par exemple, dans un workflow d’application logique, ajoutez votre connecteur personnalisé pour pouvoir créer une connexion à votre API et appeler toutes les opérations fournies par l’API de la même façon que pour des connecteurs gérés par Microsoft.

## <a name="6-share-your-connector"></a>6. Partager le connecteur 

Vous pouvez partager votre connecteur avec les utilisateurs de votre organisation de la même façon que des ressources dans Logic Apps, Flow ou PowerApps. Bien que le partage soit facultatif, certains scénarios peuvent vous amener à vouloir partager vos connecteurs avec d’autres utilisateurs.

Les connecteurs personnalisées inscrits mais non certifiés fonctionnent comme des connecteurs gérés par Microsoft, mais sont visibles et accessibles *uniquement* à l’auteur du connecteur et aux utilisateurs qui ont le même client Azure Active Directory et un abonnement Azure pour les applications logiques dans la région où sont déployées ces applications. L’étape suivante explique comment partager un connecteur avec des utilisateurs externes à l’extérieur de ces limites, par exemple, avec tous les utilisateurs Logic Apps, Flow et PowerApps.

> [!IMPORTANT]
> Si vous partagez un connecteur, d’autres personnes peuvent en devenir dépendantes. 
> ***Supprimer votre connecteur a pour conséquence la suppression de toutes les connexions à ce connecteur.***

* [Logic Apps : Partager un connecteur](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow : Partager un connecteur](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps : Partager un connecteur](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7. Certifier le connecteur

Pour partager votre connecteur avec tous les utilisateurs Logic Apps, Flow et PowerApps (facultatif), soumettez-le à la certification Microsoft. Ce processus passe en revue le connecteur, vérifie la conformité technique et celle du contenu, et valide les fonctionnalités de Logic Apps, Flow et PowerApps afin que Microsoft puisse le publier. Découvrez [comment soumettre un connecteur à la certification Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Obtenir de l'aide

* Pour obtenir de l’aide concernant l’intégration et le développement, envoyez un e-mail à [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). Microsoft surveille activement les problèmes et les questions des développeurs sur ce compte, et les transfère à l’équipe concernée. 

* Vous trouverez les questions fréquentes sur la page [FAQ des connecteurs personnalisés](../logic-apps/custom-connector-faq.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer un connecteur personnalisé à partir d’une API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Configurer l’authentification pour les connecteurs personnalisés](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Décrire les connecteurs personnalisés avec des collections Postman](../logic-apps/custom-connector-api-postman-collection.md)
* [Soumettre des connecteurs personnalisés à la certification Microsoft](../logic-apps/custom-connector-submit-certification.md)
