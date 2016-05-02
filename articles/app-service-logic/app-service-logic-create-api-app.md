<properties 
	pageTitle="Créer une API pour Logic Apps" 
	description="Création d’une API personnalisée à utiliser avec Logic Apps" 
	authors="jeffhollan" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"	
	ms.topic="article"
	ms.date="04/05/2016"
	ms.author="jehollan"/>
    
# Création d’une API personnalisée à utiliser avec Logic Apps

Si vous souhaitez étendre la plate-forme d’applications logiques, il existe de nombreuses façons d’appeler des API ou des systèmes qui ne sont pas disponibles en tant qu’un de nos nombreux connecteurs prêts à l’emploi. Une de ces méthodes consiste à créer une application API que vous pouvez appeler à partir d’un flux workflow d’application logique.

## Outils utiles

Pour que les API fonctionnent de manière optimale avec les applications logiques, nous vous recommandons de générer un document [swagger](http://swagger.io) détaillant les opérations prises en charge et les paramètres de votre API. Il existe de nombreuses bibliothèques (telles que [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) qui génèrent automatiquement le swagger pour vous. Vous pouvez également utiliser [TRex](https://github.com/nihaue/TRex) pour permettre d’annoter le swagger afin d’améliorer son fonctionnement avec les applications logiques (noms d’affichage, types de propriété, etc.). Pour voir certains exemples d’API Apps conçues pour les applications logiques, veuillez consulter notre [référentiel GitHub](http://github.com/logicappsio) ou [blog](http://aka.ms/logicappsblog).

## Actions

L’action de base pour une application logique est un contrôleur qui acceptera une demande HTTP et renverra une réponse (généralement 200). Cependant, il existe différents modèles qu’il est possible de suivre pour étendre des actions en fonction de vos besoins.

Par défaut, le moteur de l’application logique fait expirer une demande après 1 minute. Toutefois, vous pouvez faire en sorte que votre API s’exécute sur des actions plus longues et que le moteur attende leur fin en suivant un modèle webhook ou asynchrone détaillé ci-dessous.

### Actions longues - modèle asynchrone

Lorsque vous exécutez une étape ou une tâche de longue durée, la première chose à faire est de s’assurer le moteur sait que l’expiration n’a pas encore eu lieu. Vous devez également indiquer au moteur comment il saura que vous avez terminé la tâche, et enfin, vous devrez lui renvoyer les données pertinentes afin qu’il poursuive le workflow. Vous pouvez effectuer cette opération par le biais d’une API en suivant le flux ci-après. Ces étapes sont réalisées à partir de l’API personnalisée :

1\. lorsqu’une demande est reçue, renvoyez immédiatement une réponse (avant que le travail soit effectué). Il s’agira d’une réponse `202 ACCEPTED` informant le moteur que vous avez obtenu les données, accepté la charge utile et que le processus est en cours de traitement. La méthode 202 doit contenir les en-têtes suivants :
 * En-tête `location` (requis) : il s’agit d’un chemin d’accès absolu à l’URL qu’API Apps peut utiliser pour vérifier l’état du travail.
 * `retry-after` (facultatif, défini par défaut sur 20 pour les actions). Il s’agit du nombre de secondes pendant lequel le moteur doit attendre avant d’interroger l’URL de l’en-tête d’emplacement pour vérifier l’état.

2\. Une fois l’état d’un travail vérifié, effectuez les vérifications suivantes :
 * Si le travail est effectué : renvoyez une réponse `200 OK`, avec la charge utile de réponse.
 * Si le travail est encore en cours de traitement : renvoyez une autre réponse `202 ACCEPTED`, avec les mêmes en-têtes que la réponse initiale

Ce modèle vous permet d’exécuter des tâches extrêmement longues dans un thread de votre API personnalisée, tout en maintenant une connexion active avec le moteur Logic Apps afin qu’il n’expire pas ou ne continue pas avant que le travail soit terminé. Lorsque vous ajoutez ceci à votre application logique, il est important de noter que vous n’avez pas à modifier votre définition afin que l’application logique continue à interroger et à vérifier l’état. Dès que le moteur voit une réponse 202 ACCEPTÉ avec un en-tête d’emplacement valide, il respecte le modèle asynchrone et continuer d’interroger l’en-tête d’emplacement jusqu’à ce qu’une réponse autre que 202 soit renvoyée.

Vous pouvez voir [ici](https://github.com/jeffhollan/LogicAppsAsyncResponseSample) un exemple de ce modèle dans GitHub

### Actions Webhook

Au cours de votre workflow, vous pouvez suspendre l’application logique et attendre un « rappel » pour continuer. Ce rappel intervient sous la forme d’une requête HTTP POST. Pour implémenter ce modèle, vous devez fournir deux points de terminaison sur votre contrôleur : Subscribe et Unsubscribe.

Sur Subscribe, l’application logique crée et enregistre une URL de rappel que votre API peut stocker et rappeler sous la forme d’une requête HTTP POST. N’importe quel contenu/en-tête est transmis à l’application logique et peut être utilisé dans le reste du workflow. Le moteur de l’application logique appelle le point Subscribe à l’exécution dès qu’il atteint cette étape.

Si l’exécution a été annulée, le moteur de l’application logique appelle le point de terminaison Unsubscribe. Votre API peut ensuite annuler l’inscription de l’URL de rappel, le cas échéant.

Actuellement le concepteur d’applications logiques ne gère pas la détection d’un point de terminaison Webhook par le biais de Swagger. Par conséquent, vous devez ajouter l’action « Webhook » et spécifiez l’URL, les en-têtes et le corps de votre requête pour utiliser ce type d’action. Vous pouvez utiliser la fonction de workflow `@listCallbackUrl()` dans un de ces champs en fonction des besoins afin de transmettre l’URL de rappel.

Vous pouvez voir [ici](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs) un exemple de modèle webhook dans GitHub

## Déclencheurs

En plus des actions, vous pouvez faire en sorte que votre API personnalisée agisse comme déclencheur d’une application logique. Vous pouvez suivre les deux modèles ci-dessous pour déclencher une application logique :

### Déclencheurs d’interrogation

Les déclencheurs d’interrogation agissent plus comme les actions asynchrones longue durée ci-dessus. Le moteur de l’application logique appelle le point de terminaison du déclencheur après qu’un certain laps de temps se soit écoulé (en fonction de la référence (SKU), 15 secondes pour le niveau Premium, 1 minute pour le niveau Standard et 1 pour le niveau gratuit).

Si aucune donnée n’est disponible, le déclencheur renvoie une réponse `202 ACCEPTED`, avec un en-tête `location` et `retry-after`. Toutefois, pour les déclencheurs, il est recommandé que l’en-tête `location` contienne un paramètre de requête `triggerState`. Il s’agit d’un identificateur qui permet à votre API de savoir quand l’application logique a été déclenchée pour la dernière fois. Si des données sont disponibles, le déclencheur renvoie une réponse `200 OK`, avec la charge utile de contenu. Cette action déclenche l’application logique.

Par exemple, en cours d’interrogation pour savoir si un fichier est disponible, vous pouvez concevoir un déclencheur d’interrogation qui effectuerait les opérations suivantes :

* Si une demande a été reçue sans triggerState, l’API renvoie une réponse `202 ACCEPTED` avec un en-tête `location` dont le triggerState correspond à l’heure actuelle et le `retry-after` est 15.
* Si une demande a été reçue avec un triggerState :
 * Vérifiez si tous les fichiers ont été ajoutés après le triggerState DateTime. 
  * S’il existe 1 fichier, renvoyez une réponse `200 OK` avec la charge utile de contenu, incrémentez le triggerState à la DateTime du fichier renvoyé et définissez le `retry-after` sur 15.
  * S’il existe plusieurs fichiers, je peux en renvoyer un à la fois avec un `200 OK`, incrémenter mon triggerState dans l’en-tête `location` et définir `retry-after` sur 0. Cela permet d’informer le moteur que d’autres données sont disponibles et de procéder immédiatement à une requête au niveau de l’en-tête `location` spécifié.
  * Si aucun fichier n’est disponible, renvoyez une réponse `202 ACCEPTED` et ne modifiez pas le triggerState `location`. Définissez `retry-after` sur 15.

Vous pouvez voir [ici](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers) un exemple de déclencheur d’interrogation dans GitHub

### Déclencheurs Webhook

Les déclencheurs Webhook agissent plus comme les actions Webhook ci-dessus. Le moteur de l’application logique appelle le point de terminaison Subscribe chaque fois qu’un déclencheur webhook est ajouté et enregistré. Votre API peut enregistrer l’URL webhook et l’appeler via HTTP POST dès que des données sont disponibles. La charge utile de contenu et les en-têtes sont transmis dans l’exécution de l’application logique.

Si un déclencheur webhook est supprimé (soit l’application logique dans son intégralité ou juste le déclencheur webhook), le moteur appelle l’URL Unsubscribe où votre API peut annuler l’inscription de l’URL de rappel et arrêter n’importe quel processus en fonction des besoins.

Actuellement, le concepteur d’applications logiques ne gère pas la détection d’un point de terminaison webhook par le biais de Swagger. Par conséquent, vous devez ajouter le déclencheur « Webhook » et spécifiez l’URL, les en-têtes et le corps de votre requête pour utiliser ce type d’action. Vous pouvez utiliser la fonction de workflow `@listCallbackUrl()` dans un de ces champs en fonction des besoins afin de transmettre l’URL de rappel.

Vous pouvez voir [ici](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers) un exemple de déclencheur webhook dans GitHub

<!---HONumber=AcomDC_0420_2016-->