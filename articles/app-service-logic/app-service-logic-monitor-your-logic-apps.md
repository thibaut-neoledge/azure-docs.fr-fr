<properties 
	pageTitle="Analyser vos applications logiques dans Azure App Service | Microsoft Azure" 
	description="Comment voir ce que vos applications logiques ont fait" 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="jehollan"/>

# Analyser vos applications logiques

Une fois que vous avez [créé une application logique](app-service-logic-create-a-logic-app.md), vous pouvez consulter l’historique complet de son exécution dans le Portail Azure. Vous pouvez également configurer des services tels que les diagnostics Azure et les alertes Azure pour analyser les événements en temps réel, et vous alerter en cas d’événements du type « échec de plus de 5 exécutions en une heure ».

## Analyser dans le portail Azure

Pour afficher l’historique, sélectionnez **Parcourir**, puis **Applications logiques**. La liste de toutes les applications logiques de votre abonnement s’affiche. Sélectionnez l’application logique que vous souhaitez analyser. La liste de l’ensemble des actions et des déclencheurs qui se sont produits pour cette application logique s’affiche.

![Vue d'ensemble](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Voici quelques sections de ce panneau qui sont utiles :

- La section **Résumé** répertorie **Toutes les exécutions** et **l’Historique du déclencheur**.
	- La sous-section **Toutes les exécutions** répertorie les dernières exécutions de l’application logique. Vous pouvez cliquer sur une ligne pour obtenir des informations sur l’exécution en question ou cliquer sur la mosaïque pour afficher d’autres exécutions.
	- La sous-section **Historique du déclencheur** répertorie toutes les activités de déclencheur de cette application logique. Les activités de déclencheur peuvent être les suivantes : une vérification « ignorée » de l’ajout de nouvelle données (par exemple pour voir si un nouveau ficher a été ajouté à FTP) ; « Opération réussie » signifie que des données ont été retournées pour déclencher une application logique ou « Échec » correspond à une erreur de la configuration.
- La section **Diagnostics** vous permet d’afficher les détails et les événements d’exécution, et de vous abonner aux [alertes Azure](#adding-azure-alerts).

### Afficher les détails d’exécution

Cette liste d’exécutions indique **l’État**, **l’Heure de début** et la **Durée** de l’exécution spécifique. Sélectionnez n’importe quelle ligne pour afficher les détails de cette exécution.

L’affichage de l’analyse vous montre chaque étape de l’exécution, les entrées et sorties ainsi que les éventuels messages d’erreur.

![Exécution et actions](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

Si vous avez besoin de détails supplémentaires comme **l’ID de corrélation** de l’exécution (qui peut être utilisé pour l’API REST), vous pouvez cliquer sur le bouton **Détails de l’exécution**. Cela inclut l’ensemble des étapes, l’état ainsi que les entrées/sorties de l’exécution.

## Diagnostics et alertes Azure

Outre les détails fournis par le portail Azure et l’API REST ci-dessus, vous pouvez configurer votre application logique afin d’utiliser les diagnostics Azure pour obtenir des détails et un débogage enrichis.

1. Cliquez sur la section **Diagnostics** du panneau de l’application logique.
1. Cliquez pour configurer les **Paramètres de diagnostic**.
1. Configurez un Event Hub ou un compte de stockage vers lequel émettre des données.

	![Paramètres des diagnostics Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### Ajout d’alertes Azure

Une fois les diagnostics configurés, vous pouvez ajouter des alertes Azure qui se déclenchent lorsque certains seuils sont dépassés. Dans le panneau **Diagnostics**, sélectionnez la mosaïque **Alertes** et **Ajouter une alerte**. Vous êtes alors guidé dans la configuration d’une alerte en fonction du nombre de seuils et de métriques.

![Métriques d’alerte Azure](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

Vous pouvez configurer les sous-sections **Condition**, **Seuil** et **Période** comme vous le souhaitez. Pour finir, vous pouvez configurer une adresse de messagerie à laquelle envoyer une notification, ou configurer un webhook. Vous pouvez utiliser le [déclencheur de requête](../connectors/connectors-native-reqres.md) dans une application logique pour qu’il s’exécute également sur une alerte (pour effectuer des opérations comme [publier dans Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app), [envoyer un texte](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app) ou [ajouter un message à une file d’attente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)).

### Paramètres des diagnostics Azure

Chacun de ces événements contient des détails sur l’application logique et l’événement en question comme l’état. Voici un exemple d’événement *ActionCompleted* :

```javascript
{
			"time": "2016-07-09T17:09:54.4773148Z",
			"workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
			"resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
			"category": "WorkflowRuntime",
			"level": "Information",
			"operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
			"properties": {
				"$schema": "2016-06-01",
				"startTime": "2016-07-09T17:09:53.4336305Z",
				"endTime": "2016-07-09T17:09:53.5430281Z",
				"status": "Succeeded",
				"code": "OK",
				"resource": {
					"subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
					"resourceGroupName": "MyResourceGroup",
					"workflowId": "cff00d5458f944d5a766f2f9ad142553",
					"workflowName": "MyLogicApp",
					"runId": "08587361146922712057",
					"location": "eastus",
					"actionName": "Http"
				},
				"correlation": {
					"actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
					"clientTrackingId": "my-custom-tracking-id"
				},
				"trackedProperties": {
					"myProperty": "<value>"
				}
			}
		}
```

*clientTrackingId* et *trackedProperties* sont les deux propriétés qui sont particulièrement utiles pour le suivi et l’analyse.

#### ID de suivi client

L’ID de suivi client est une valeur qui met en corrélation les événements lors de l’exécution d’une application logique, notamment tous les flux de travail imbriqués appelés dans le cadre d’une application logique. Cet ID est généré automatiquement s’il n’est pas fourni, mais vous pouvez le spécifier manuellement à partir d’un déclencheur en transmettant un en-tête `x-ms-client-tracking-id` avec la valeur d’ID dans la requête de déclencheur (déclencheur de requête, déclencheur HTTP ou déclencheur webhook).

#### Propriétés suivies

Les propriétés suivies peuvent être ajoutées à des actions dans la définition de flux de travail pour effectuer le suivi des entrées ou des sorties dans les données de diagnostic. Ce peut être utile si vous souhaitez effectuer le suivi des données comme un ID de commande dans vos données de télémétrie. Pour ajouter une propriété suivie, insérez la propriété `trackedProperties` dans une action. Les propriétés suivies ne peuvent suivre que les entrées ou sorties des actions uniques, mais vous pouvez utiliser les propriétés `correlation` des événements à mettre en corrélation entre les actions d’une exécution.

```javascript
{
	"myAction": {
		"type": "http",
		"inputs": {
			"uri": "http://uri",
			"headers": {
				"Content-Type": "application/json"
			},
			"body": "@triggerBody()"
		},
		"trackedProperties":{
			"myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
			"myActionHTTPValue": "@action()['outputs']['body']['foo']",
			"transactionId": "@action()['inputs']['body']['bar']"
		}
	}
}
```

### Extension de vos solutions

Vous pouvez tirer parti de ces données de télémétrie de l’Event Hub ou du compte de stockage dans d’autres services comme [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) et [Power BI](https://powerbi.com) pour bénéficier de l’analyse en temps réel de vos flux de travail d’intégration.

## Étapes suivantes
- [Exemples et scénarios courants pour les applications logiques](app-service-logic-examples-and-scenarios.md)
- [Création d’un modèle de déploiement d’applications logiques](app-service-logic-create-deploy-template.md)
- [Fonctionnalités d’intégration d'entreprise](app-service-logic-enterprise-integration-overview.md)

<!---HONumber=AcomDC_0803_2016-->