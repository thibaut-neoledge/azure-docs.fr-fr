---
title: "Créer des API web et des API REST en tant que connecteurs - Azure Logic Apps | Microsoft Docs"
description: "Créer des API web et des API REST pour appeler vos API, vos services ou vos systèmes dans les flux de travail d’intégration système avec Azure Logic Apps"
keywords: "API web, API REST, connecteurs, flux de travail, intégration système"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/26/2017
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 4ae98804aced23c0261c1d58721cb18d8152c6f1
ms.contentlocale: fr-fr
ms.lasthandoff: 06/08/2017

---

# <a name="create-custom-apis-as-connectors-for-logic-apps"></a>Créer des API personnalisées en tant que connecteurs pour les applications logiques

Bien qu’Azure Logic Apps offre [plus de 100 connecteurs intégrés](../connectors/apis-list.md), que vous pouvez utiliser dans les flux de travail d’application logique, vous voudrez peut-être appeler des API, des systèmes et des services qui ne sont pas disponibles en tant que connecteurs. Vous pouvez créer vos propres API personnalisées qui fournissent des actions et des déclencheurs à utiliser dans les applications logiques. Voici d’autres raisons pour lesquelles vous pouvez vouloir créer vos propres API à utiliser en tant que connecteurs dans les applications logiques :

* Étendre l’intégration de votre système actuel et les flux de travail d’intégration de données.
* Aider les clients à utiliser votre service pour gérer des tâches personnelles ou professionnelles.
* Étendre la portée, la détectabilité et l’utilisation de votre service.

En principe, les connecteurs sont des API web qui utilisent REST pour les interfaces enfichables, le [format de métadonnées Swagger](http://swagger.io/specification/) pour la documentation et JSON en tant que format d’échange de données. Étant donné que les connecteurs sont des API REST qui communiquent via des points de terminaison HTTP, vous pouvez utiliser n’importe quel langage, comme .NET, Java ou Node.js, pour la création de connecteurs. Vous pouvez également héberger votre API sur [Azure App Service](../app-service/app-service-value-prop-what-is.md), une offre de plateforme en tant que service (PaaS) qui fournit une des manières les plus optimales, les plus simples et les plus évolutives d’héberger des API. 

Pour que les API personnalisées fonctionnent avec les applications logiques, votre API peut fournir des [*actions*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) qui effectuent des tâches spécifiques dans les flux de travail des applications logiques. Votre API peut également faire office de [*déclencheur*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) qui démarre le flux de travail d’une application logique lorsque de nouvelles données ou un événement répondent à une condition donnée. Cette rubrique décrit les modèles courants que vous pouvez suivre pour la création d’actions et de déclencheurs dans votre API, en fonction du comportement que vous souhaitez que votre API adopte.

> [!TIP] 
> Bien que vous puissiez déployer votre API comme les [applications web](../app-service-web/app-service-web-overview.md), envisagez de la déployer comme les [applications API](../app-service-api/app-service-api-apps-why-best-platform.md), ce qui peut simplifier votre travail lorsque vous générez, hébergez et consommez des API dans le cloud et en local. Vous n’êtes pas obligé de modifier du code dans votre API. Il vous suffit de déployer votre code dans une application API. Découvrez comment [créer des applications API avec ASP.NET](../app-service-api/app-service-api-dotnet-get-started.md), [Java](../app-service-api/app-service-api-java-api-app.md), ou [Node.js](../app-service-api/app-service-api-nodejs-api-app.md). 
>
> Pour obtenir des exemples d’application API générées pour les applications logiques, consultez le [référentiel GitHub Azure Logic Apps](http://github.com/logicappsio) ou le [blog](http://aka.ms/logicappsblog).

## <a name="helpful-tools"></a>Outils utiles

Une API personnalisée fonctionne mieux avec les applications logiques lorsqu’elle dispose également d’un [document Swagger](http://swagger.io/specification/) qui décrit ses opérations et ses paramètres.
De nombreuses bibliothèques (telles que [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) permettent de générer automatiquement le fichier Swagger pour vous. Pour annoter le fichier Swagger avec les noms d’affichage, les types de propriété, etc., vous pouvez également utiliser [TRex](https://github.com/nihaue/TRex) afin que votre fichier Swagger fonctionne bien avec les applications logiques.

<a name="actions"></a>

## <a name="action-patterns"></a>Modèles d’action

Pour que les applications logiques effectuent des tâches, votre API personnalisée doit fournir des [*actions*](./logic-apps-what-are-logic-apps.md#logic-app-concepts). Chaque opération dans votre API renvoie à une action. Une action de base peut être un contrôleur qui accepte les requêtes HTTP et renvoie des réponses HTTP. Par exemple, une application logique envoie une requête HTTP à votre application web ou à votre application API. Votre application renvoie alors une réponse HTTP, ainsi que du contenu que l’application logique peut traiter.

Pour une action standard, vous pouvez écrire une méthode de requête HTTP dans votre API et décrire cette méthode dans un fichier Swagger. Vous pouvez ensuite appeler votre API directement avec une [action HTTP](../connectors/connectors-native-http.md) ou une action [HTTP + Swagger](../connectors/connectors-native-http-swagger.md). Par défaut, les réponses doivent être renvoyées dans le [délai d’expiration de la requête](./logic-apps-limits-and-config.md). 

![Modèle d’action standard](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Pour qu’une application logique attende pendant que votre API termine des tâches plus longues, votre API peut suivre le [modèle d’interrogation asynchrone](#async-pattern) ou le [modèle Webhook asynchrone](#webhook-actions), tous deux décrits dans cette rubrique. Pour comprendre les différences entre ces modèles, prenons l’exemple de la commande d’un gâteau personnalisé dans une boulangerie. Le modèle d’interrogation reviendrait à appeler la boulangerie toutes les 20 minutes pour vérifier si le gâteau est prêt. Le modèle Webhook correspond au cas où la boulangerie vous demande votre numéro de téléphone afin de vous appeler quand le gâteau est prêt.

Pour obtenir des exemples, visitez le [référentiel GitHub d’applications logiques](https://github.com/logicappsio). Découvrez également les [mesures d’utilisation des actions](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Effectuer des tâches longues avec le modèle d’action d’interrogation

Pour que votre API effectue des tâches qui peuvent durer plus longtemps que le [délai d’expiration des requêtes](./logic-apps-limits-and-config.md), vous pouvez utiliser le modèle d’interrogation asynchrone. Ce modèle force votre API à travailler dans un thread distinct, mais conserve une connexion active au moteur Logic Apps. De cette façon, l’application logique n’expire pas ou passe à l’étape suivante dans le flux de travail avant que votre API termine sa tâche.

Voici le modèle général :

1. Assurez-vous que le moteur sait que votre API a accepté la requête et a commencé à travailler.
2. Lorsque le moteur demande ensuite plusieurs fois l’état du travail, indiquez au moteur lorsque votre API a terminé sa tâche.
3. Renvoyez les données pertinentes au moteur afin que le flux de travail de l’application logique puisse continuer.

<a name="bakery-polling-action"></a> Revenons maintenant à l’analogie de la boulangerie pour le modèle d’interrogation et imaginez que vous appelez une boulangerie pour commander un gâteau personnalisé en livraison. La confection du gâteau prend du temps, et vous n’avez pas envie d’attendre au téléphone pendant que le boulanger travaille. La boulangerie confirme votre commande et vous dit d’appeler toutes les 20 minutes pour connaître l’avancement de la commande. Après 20 minutes, vous appelez la boulangerie, qui vous indique que votre gâteau n’est pas prêt et que vous devez rappeler dans 20 minutes. Ces aller-retours se poursuivent jusqu'à ce que vous appeliez, que la boulangerie vous dise que votre commande est prête et qu’elle vous livre le gâteau. 

Revenons à ce modèle d’interrogation pour votre API. La boulangerie représente votre API personnalisée, tandis que vous, le client achetant le gâteau, représentez le moteur Logic Apps. Lorsque le moteur appelle votre API avec une requête, votre API confirme la requête et répond avec l’intervalle de temps pendant lequel le moteur peut vérifier l’état du travail. Le moteur continue la vérification du statut jusqu'à ce que votre API réponde que le travail est effectué et renvoie des données à votre application logique, qui poursuit ensuite le flux de travail. 

![Modèle d’action d’interrogation](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Voici les étapes spécifiques que votre API doit suivre, décrites du point de vue de l’API :

1. Lorsque votre API reçoit une requête HTTP lui demandant de commencer à travailler, renvoyez immédiatement une réponse HTTP `202 ACCEPTED` avec l’en-tête `location` décrit plus loin dans cette étape. Cette réponse informe le moteur Logic Apps que votre API a reçu la requête, a accepté la charge utile de la requête (entrée de données) et est en train de la traiter. 
   
   La réponse `202 ACCEPTED` doit inclure ces en-têtes :
   
   * *Requis* : un en-tête `location` qui spécifie le chemin d’accès absolu à une URL permettant au moteur Logic Apps de vérifier l’état du travail de votre API

   * *Facultatif* : un en-tête `retry-after` qui spécifie le nombre de secondes pendant lesquelles le moteur doit attendre avant de vérifier l’URL `location` pour connaître l’état du travail. 

     Par défaut, le moteur vérifie toutes les 20 secondes. Pour spécifier un intervalle différent, incluez l’en-tête `retry-after` et le nombre de secondes jusqu'à l’interrogation suivante.

2. Une fois le temps indiqué écoulé, le moteur Logic Apps interroge l’URL `location` pour vérifier l’état du travail. Votre API doit effectuer ces vérifications et renvoyer ces réponses :
   
   * Si le travail est effectué, renvoyez la réponse HTTP `200 OK`, ainsi que la charge utile de réponse (entrée pour l’étape suivante).

   * Si le travail est encore en cours de traitement, renvoyez une autre réponse HTTP `202 ACCEPTED`, avec les mêmes en-têtes que la réponse d’origine.

Lorsque votre API suit ce modèle, vous n’avez rien à modifier dans la définition du flux de travail d’application logique pour poursuivre la vérification du statut du travail. Lorsque le moteur reçoit une réponse HTTP `202 ACCEPTED` et un en-tête `location` valide, le moteur respecte le modèle asynchrone et vérifie l’en-tête `location` jusqu'à ce que votre API renvoie une réponse autre que 202.

> [!TIP]
> Pour un exemple de modèle asynchrone, consultez cet [exemple de réponse de contrôleur asynchrone dans GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Effectuer des tâches longues avec le modèle d’action Webhook

Pour les tâches longues et le traitement asynchrone, vous avez également la possibilité d’utiliser le modèle Webhook. Ce modèle met en pause l’application logique afin qu’elle attende un « rappel » de votre API pour terminer le traitement avant de poursuivre le flux de travail. Ce rappel est un élément POST HTTP qui envoie un message vers une URL quand un événement se produit. 

<a name="bakery-webhook-action"></a> Revenons maintenant à l’analogie de la boulangerie pour le modèle Webhook et imaginez que vous appelez une boulangerie pour commander un gâteau personnalisé en livraison. La confection du gâteau prend du temps, et vous n’avez pas envie d’attendre au téléphone pendant que le boulanger travaille. La boulangerie confirme votre commande, mais cette fois, vous donnez votre numéro de téléphone afin d’être rappelé quand le gâteau est terminé. Cette fois, la boulangerie vous prévient lorsque votre commande est prête et vous livre le gâteau.

Appliquons cet exemple à votre API : la boulangerie représente votre API personnalisée, tandis que vous, le client achetant le gâteau, représentez le moteur Logic Apps. Le moteur appelle votre API avec une requête et inclut une URL de « rappel ».
Lorsque le travail est terminé, votre API utilise l’URL pour en informer le moteur et renvoyer les données à votre application logique, avant de continuer le flux de travail. 

Pour ce modèle, définissez deux points de terminaison sur votre contrôleur : `subscribe` et `unsubscribe`

*  Point de terminaison `subscribe` : lorsque l’exécution atteint l’action de votre API dans le flux de travail, le moteur Logic Apps appelle le point de terminaison `subscribe`. Cette étape entraîne la création par l’application logique d’une URL de rappel que votre API enregistre, avant d’attendre le rappel de votre API lorsque le travail est terminé. Votre API envoie ensuite un rappel avec un élément POST HTTP à l’URL et transmet à l’application logique les en-têtes et le contenu renvoyés en tant qu’entrées.

* Point de terminaison `unsubscribe` : si l’exécution de l’application logique est annulée, le moteur Logic Apps appelle le point de terminaison `unsubscribe`. Votre API peut alors annuler l’inscription de l’URL de rappel, et arrêter tous les processus selon les besoins.

![Modèle d’action Webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Actuellement, le Concepteur d’application logique ne prend pas en charge la découverte des points de terminaison Webhook via Swagger. Pour ce modèle, vous devez donc ajouter une action [**Webhook**](../connectors/connectors-native-webhook.md) et spécifier l’URL, les en-têtes et le corps de votre requête. Voir également [Actions et déclencheurs de flux de travail](logic-apps-workflow-actions-triggers.md#api-connection-webhook-action). Pour transmettre l’URL de rappel, vous pouvez utiliser la fonction de flux de travail `@listCallbackUrl()` dans un de ces champs en fonction des besoins.

> [!TIP]
> Pour un exemple de modèle Webhook, consultez cet [exemple de déclencheur Webhook dans GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Modèles de déclencheur

Votre API personnalisée peut faire office de [*déclencheur*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) qui démarre une application logique lorsque de nouvelles données ou un événement répondent à une condition donnée. Ce déclencheur peut soit vérifier régulièrement, soit attendre et écouter, la présence de nouvelles données ou d’événements au niveau du point de terminaison de votre service. Si de nouvelles données ou un événement remplissent la condition indiquée, le déclencheur est activé et démarre l’application logique, qui est à l’écoute de ce déclencheur. Pour démarrer les applications logiques de cette manière, votre API peut suivre le modèle de [*déclencheur d’interrogation*](#polling-triggers) ou de [*déclencheur Webhook*](#webhook-triggers). Ces modèles sont semblables à leurs équivalents : [actions d’interrogation](#async-pattern) et [actions Webhook](#webhook-actions). Découvrez également les [mesures d’utilisation des déclencheurs](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Rechercher régulièrement les nouvelles données ou les événements avec le modèle de déclencheur d’interrogation

Un *déclencheur d’interrogation* fonctionne presque comme [l’action d’interrogation](#async-pattern) décrite précédemment dans cette rubrique. Le moteur Logic Apps appelle régulièrement et recherche de nouvelles données ou des événements au niveau du point de terminaison du déclencheur. Si le moteur trouve de nouvelles données ou un événement qui répond à la condition spécifiée, le déclencheur est activé. Le moteur crée alors une instance d’application logique qui traite les données en tant qu’entrée. 

![Modèle de déclencheur d’interrogation](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Chaque requête d’interrogation compte comme une exécution de l’action, même lorsqu’aucune instance d’application logique n’est créée. Pour éviter que les mêmes données soient traitées plusieurs fois, votre déclencheur doit nettoyer les données qu’il a déjà lues et transmises à l’application logique.

Voici les étapes spécifiques d’un déclencheur d’interrogation, décrites du point de vue de l’API :

| Il existe de nouvelles données ou un nouvel événement ?  | Réponse de l’API | 
| ------------------------- | ------------ |
| Trouvé | Renvoyez un état HTTP `200 OK` avec la charge utile de réponse (entrée pour l’étape suivante). <br/>Cette réponse crée une instance d’application logique et démarre le flux de travail. |
| Introuvable | Renvoyez un état HTTP `202 ACCEPTED` avec un en-tête `location` et un en-tête `retry-after`. <br/>Pour les déclencheurs, l’en-tête `location` doit également contenir un paramètre de requête `triggerState`, qui est généralement un « timestamp ». Votre API peut utiliser cet identificateur pour vérifier quand l’application logique a été déclenchée pour la dernière fois. |

Par exemple, pour vérifier périodiquement la présence de nouveaux fichiers dans votre service, vous pouvez générer un déclencheur d’interrogation qui possède ces comportements :

| La requête inclut `triggerState` ? | Réponse de l’API |
| -------------------------------- | -------------|
| Non | Renvoyez un état HTTP `202 ACCEPTED` plus un en-tête `location` avec `triggerState` défini sur l’heure actuelle et l’intervalle `retry-after` sur 15 secondes. |
| Oui | Vérifiez la présence de fichiers ajoutés après le `DateTime` pour `triggerState` dans votre service. |

| Nombre de fichiers trouvés | Réponse de l’API |
| --------------------- | -------------|
| Fichier unique | Renvoyez un état HTTP `200 OK` et la charge utile du contenu, mettez à jour `triggerState` vers `DateTime` pour le fichier renvoyé et définissez l’intervalle `retry-after` sur 15 secondes. |
| Fichiers multiples | Renvoyez un seul fichier à la fois et un état HTTP `200 OK`, mettez à jour `triggerState` et définissez l’intervalle `retry-after` sur 0 seconde. </br>Ces étapes informent le moteur que des données supplémentaires sont disponibles, et qu’il doit demander immédiatement des données à partir de l’URL dans l’en-tête `location`. |
| Aucun fichier | Renvoyez un état HTTP `202 ACCEPTED`, ne modifiez pas `triggerState` et définissez l’intervalle `retry-after` sur 15 secondes. |

> [!TIP]
> Pour obtenir un exemple de modèle de déclencheur d’interrogation, consultez cet [exemple de contrôleur de déclencheur d’interrogation dans GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Attendre et écouter les nouvelles données ou les événements avec le modèle de déclencheur Webhook

Un déclencheur Webhook est un *déclencheur d’émission* qui attend et écoute les nouvelles données ou les événements au point de terminaison de votre service. Si de nouvelles données ou un événement remplissent la condition spécifiée, le déclencheur est activé et crée une instance d’application logique, qui traite ensuite les données en tant qu’entrée.
Les déclencheurs Webhook agissent de façon très similaire aux [actions Webhook](#webhook-actions) précédemment décrites dans cette rubrique. Ils sont configurés avec les points de terminaison `subscribe` et `unsubscribe`. 

* Point de terminaison `subscribe` : lorsque vous ajoutez et enregistrez un déclencheur Webhook dans votre application logique, le moteur Logic Apps appelle le point de terminaison `subscribe`. Cette étape entraîne la création par l’application logique d’une URL de rappel que votre API enregistre. Lorsque de nouvelles données ou un événement remplissent la condition spécifiée, votre API envoie un rappel avec un élément POST HTTP à l’URL. La charge utile de contenu et les en-têtes sont transmis à l’application logique en tant qu’entrée.

* Point de terminaison `unsubscribe` : si le déclencheur Webhook ou l’intégralité de l’application logique est supprimé, le moteur Logic Apps appelle le point de terminaison `unsubscribe`. Votre API peut alors annuler l’inscription de l’URL de rappel, et arrêter tous les processus selon les besoins.

![Modèle de déclencheur Webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Actuellement, le Concepteur d’application logique ne prend pas en charge la découverte des points de terminaison Webhook via Swagger. Pour ce modèle, vous devez donc ajouter un déclencheur [**Webhook**](../connectors/connectors-native-webhook.md) et spécifier l’URL, les en-têtes et le corps de votre requête. Voir également [Déclencheur HTTPWebhook](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Pour transmettre l’URL de rappel, vous pouvez utiliser la fonction de flux de travail `@listCallbackUrl()` dans un de ces champs en fonction des besoins.
>
> Pour éviter que les mêmes données soient traitées plusieurs fois, votre déclencheur doit nettoyer les données qu’il a déjà lues et transmises à l’application logique.

> [!TIP]
> Pour un exemple de modèle Webhook, consultez cet [exemple de contrôleur de déclencheur Webhook dans GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="deploy-call-and-secure-custom-apis"></a>Déployer, appeler et sécuriser les API personnalisées

Une fois les API personnalisées créées, configurez-les pour le déploiement, de façon à pouvoir les appeler en toute sécurité. Découvrez comment [déployer, appeler et sécuriser les API personnalisées pour les applications logiques](./logic-apps-custom-hosted-api.md).

## <a name="publish-custom-apis-to-azure"></a>Publier des API personnalisées dans Azure

Pour rendre vos API personnalisées publiques et utilisables dans Azure, soumettez vos nominations au [programme Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/).

## <a name="get-help"></a>Obtenir de l'aide

Pour obtenir de l’aide concernant les API personnalisées, contactez [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

Pour poser des questions, répondre aux questions et voir ce que font les autres utilisateurs d’Azure Logic Apps, visitez le [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Afin d’améliorer Logic Apps ainsi que les connecteurs, votez pour des idées ou soumettez-en sur le [site de commentaires utilisateur Logic Apps](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Étapes suivantes

* [Mesure de l’utilisation des actions et des déclencheurs](logic-apps-pricing.md)
* [Gérer les types de contenu](./logic-apps-content-type.md)
* [Gérer les erreurs et exceptions](./logic-apps-exception-handling.md)
* [Sécurisation de l’accès à vos applications logiques](./logic-apps-securing-a-logic-app.md)
* [Appeler, déclencher ou imbriquer des applications logiques via des points de terminaison HTTP](./logic-apps-http-endpoint.md)
