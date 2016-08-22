<properties
	pageTitle="Configuration d’alertes Azure à envoyer à d’autres systèmes | Microsoft Azure"
	description="Rediriger des alertes Azure vers d’autres systèmes non-Azure"
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="ashwink"/>

# Comment configurer des webhooks pour les alertes

Les Webhooks permettent à l’utilisateur d’acheminer les notifications d’alerte Azure vers d’autres systèmes afin qu’elles soient traitées ou personnalisées. À titre d’exemple, citons l’acheminement de l’alerte vers des services qui peuvent gérer une demande web entrante pour envoyer des SMS, consigner des bogues ou informer une équipe par le biais de services de conversation ou de messagerie.

L’URI du webhook doit être un point de terminaison HTTP ou HTTPS valide. Le service d’alerte Azure effectue une opération POST sur le webhook spécifié, en transmettant une charge utile JSON et un schéma spécifiques.

## Configuration de webhooks par le biais du portail

Dans l’écran Créer/mettre à jour des alertes sur le [portail Azure](https://portal.azure.com/), vous pouvez ajouter ou mettre à jour l’URI du webhook.

![Ajouter une règle d’alerte](./media/insights-webhooks-alerts/Alertwebhook.png)


## Authentification

L’authentification peut être de deux types :

1. **Authentification basée sur un jeton** : dans ce cas, vous enregistrez l’URI du webhook avec un ID de jeton tel que *https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue*.
2.	**Authentification de base**, avec un ID d’utilisateur et un mot de passe : dans ce cas, vous enregistrez l’URI du webhook sous la forme *https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar*.

## Schéma de la charge utile

L’opération POST contient le schéma et la charge utile JSON suivants pour toutes les alertes basées sur des métriques.

```
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": “https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1”                                
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```

>[AZURE.NOTE] Dans notre prochaine actualisation, nous ajouterons la prise en charge des alertes liées aux événements ("conditionType":"Event").


| Champ | Obligatoire ? | Ensemble fixe de valeurs ? | Remarques |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|O|"Activated", "Resolved"|Permet de déterminer le genre d’alerte. Azure envoie automatiquement les alertes activées et résolues pour la condition définie.|
|context| O | | Contexte de l’alerte|
|timestamp| O | | Heure à laquelle l’alerte a été déclenchée. L’alerte est déclenchée dès que la métrique est lue à partir du stockage de diagnostics.|
|id | O | | Chaque règle d’alerte possède un ID unique.|
|name|O | |
|description |O | |Description de l’alerte.|
|conditionType |O |"Metric", "Event" |Deux types d’alertes sont pris en charge. L’un est basé sur une métrique, l’autre sur un événement. À l’avenir, comme nous prendrons en charge les alertes liées aux événements, utilisez cette valeur pour vérifier si l’alerte est basée sur une métrique ou sur un événement.|
|condition |O | |Les champs à vérifier associés à ce champ dépendent du champ conditionType.|
|metricName |Pour les alertes de métrique | |Nom de la métrique qui définit ce que la règle surveille|
|metricUnit |Pour les alertes de métrique |"Bytes", "BytesPerSecond" , "Count" , "CountPerSecond" , "Percent", "Seconds"|	 Unité autorisée dans la métrique Valeurs autorisées : https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx|
|metricValue |Pour les alertes de métrique | |Valeur réelle de la métrique qui a provoqué l’alerte|
|threshold |Pour les alertes de métrique | |Valeur de seuil qui active l’alerte|
|windowSize |Pour les alertes de métrique | |Période de temps qui est utilisée pour surveiller l’activité d’alerte en fonction du seuil. Doit être comprise entre 5 minutes et 1 jour. Le format de la durée répond à la norme ISO 8601.|
|timeAggregation |Pour les alertes de métrique |"Average", "Last" , "Maximum" , "Minimum" , "None", "Total" |	Détermine la façon dont les données collectées doivent être combinées au fil du temps. La valeur par défaut est Average. Valeurs autorisées : https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx|
|operator |Pour les alertes de métrique | |Opérateur utilisé pour comparer les données et le seuil.|
|subscriptionId |O | |GUID d’abonnement Azure|
|resourceGroupName |O | |Nom du groupe de ressources de la ressource affectée|
|resourceName |O | |Nom de la ressource affectée|
|resourceType |O | |Type de la ressource affectée|
|resourceId |O | |URI d’ID de ressource qui identifie cette ressource de façon unique|
|resourceRegion |O | |Région/emplacement de la ressource affectée|
|portalLink |O | |Lien direct sur le portail Azure vers la page de résumé de la ressource|
|properties |N |Facultatif |Ensemble de paires <Clé, Valeur> (c’est-à-dire Dictionary<String, String>) incluant des détails sur l’événement Le champ properties est facultatif. Dans un flux de travail basé sur une application logique ou une interface utilisateur personnalisée, les utilisateurs peuvent entrer des paires clé/valeur transmissibles par le biais de la charge utile. L’autre manière de passer des propriétés personnalisées au webhook consiste à utiliser l’URI du webhook (sous la forme de paramètres de requête).|


>[AZURE.NOTE] Vous ne pouvez pas utiliser le champ properties par le biais du portail. Dans notre prochaine version du Kit de développement logiciel (SDK) Insights, vous pourrez définir les propriétés par le biais de l’API Alert.

## Étapes suivantes

Pour plus d’informations générales, regardez la vidéo [Intégrer les alertes Azure à PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080), qui aborde les alertes Azure et les webhooks.

Pour savoir comment créer un webhook par programmation, consultez [Créer une alerte avec des webhooks à l’aide du Kit de développement logiciel (SDK) Azure Insights (C#)](https://code.msdn.microsoft.com/Create-Azure-Alerts-with-b938077a).

Une fois que vous avez configuré les webhooks et les alertes, explorez ces autres options pour lancer un script d’automatisation.

[Exécuter des scripts Azure Automation (Runbooks)](http://go.microsoft.com/fwlink/?LinkId=627081)

Utilisez des alertes Azure pour envoyer des messages à d’autres services. Utilisez les exemples de modèles suivants pour commencer.

[Utiliser une application logique pour envoyer des SMS par le biais de l’API Twilio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)

[Utiliser une application logique pour envoyer des messages Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)

[Utilisez une application logique pour envoyer des messages à une file d’attente Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=AcomDC_0810_2016-->