<properties
	pageTitle="Azure Insights : utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique. | Microsoft Azure"
	description="Découvrez comment utiliser des actions de mise à l’échelle automatique pour appeler des URL web ou envoyer des notifications par courrier électronique dans Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique dans Azure Insights

Cet article explique comment paramétrer des déclencheurs pour vous permettre d’appeler des URL web spécifiques ou d’envoyer des courriers électroniques en fonction d’actions de mise à l’échelle automatique dans Azure.

## Webhooks
Les webhooks vous permettent d’acheminer les notifications d’alerte Azure vers d’autres systèmes afin qu’elles soient post-traitées ou personnalisées. À titre d’exemple, citons l’acheminement de l’alerte vers des services qui peuvent gérer une demande web entrante pour envoyer des SMS, consigner des bogues, informer une équipe par le biais de services de conversation ou de messagerie, etc. L’URI du webhook doit être un point de terminaison HTTP ou HTTPS valide.

## Email
Un courrier électronique peut être envoyé à n’importe quelle adresse électronique valide. Les administrateurs et les coadministrateurs de l’abonnement dans lequel la règle est exécutée seront également avertis.


## Services cloud et applications web
Vous pouvez l’activer depuis le portail Azure pour les services cloud et les batteries de serveurs (applications web).

- Choisissez la métrique **scale by (mise à l’échelle par)**.

![mise à l’échelle par](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## Jeux de mise à l’échelle de machine virtuelle
Pour des machines virtuelles plus récentes basées sur ARM (jeux de mise à l’échelle de machine virtuelle), vous pouvez effectuer cette configuration à l’aide de l’API REST, de PowerShell et de l’interface de ligne de commande. Aucune interface de portail n’est disponible pour l’instant.


## Authentification dans des webhooks
Il existe deux formes d’URI d’authentification :

	1. Token-base authentication, where you save the webhook URI with a token ID as a query parameter. For example, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
	2. Basic authentication, where you use a user ID and password. For example, https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## Schéma de la charge utile du webhook de notification de mise à l’échelle automatique
Lorsque la notification de mise à l’échelle automatique est générée, les métadonnées suivantes sont incluses dans la charge utile du webhook :

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|Champ |Obligatoire ?|	Description|
|---|---|---|
|status |yes |L’état qui indique qu’une action de mise à l’échelle automatique a été générée.|
|operation|	yes |Pour une augmentation des instances, l’option est « augmenter la taille des instances » ; pour une diminution des instances, l’option est « Diminuer la taille des instances »|
|context|	yes |Le contexte de l’action de mise à l’échelle automatique|
|timestamp|	yes |Horodatage du déclenchement de l’action de mise à l’échelle automatique.|
|id |Oui|	ID ARM (Azure Resource Manager) du paramètre de mise à l’échelle automatique|
|name |Oui|	Le nom du paramètre de mise à l’échelle automatique|
|détails|	Oui |Explication de l’action exécutée par le service de mise à l’échelle automatique et de la modification du nombre d’instances|
|subscriptionId|	Oui |ID d’abonnement de la ressource cible mise à l’échelle|
|nom\_groupe\_ressources|	Oui|	Nom de groupe de ressources de la ressource cible mise à l’échelle|
|resourceName |Oui|	Nom de la ressource cible mise à l’échelle|
|type\_ressource |Oui|	Trois valeurs sont prises en charge : « microsoft.classiccompute/domainnames/slots/roles » - Rôles de service cloud, « microsoft.compute/virtualmachinescalesets » - Jeux de mise à l’échelle de machine virtuelle et « Microsoft.Web/serverfarms » - Application Web|
|resourceId |Oui|ID ARM de la ressource cible mise à l’échelle|
|portalLink |Oui |Lien du portail Azure vers la page de résumé de la ressource cible|
|oldCapacity|	Oui |Nombre d’instances (anciennes) actuel lors de l’exécution d’une action de mise à l’échelle par la mise à l’échelle automatique|
|newCapacity|	Oui |Le nouveau nombre d’instances auquel la mise à l’échelle automatique a mis la ressource à l’échelle|
|Propriétés|	Non|	facultatif. Jeu de <Key  Value> paires (par exemple, Dictionnaire <String  String>). Le champ properties est facultatif. Dans un flux de travail basé sur une application logique ou une interface utilisateur personnalisée, vous pouvez entrer des clés et des valeurs transmissibles par le biais de la charge utile. Une autre manière de transmettre des propriétés personnalisées au webhook sortant consiste à utiliser l’URI du webhook (sous la forme de paramètres de requête).|

<!---HONumber=AcomDC_0330_2016-->