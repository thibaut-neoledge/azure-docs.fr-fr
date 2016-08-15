<properties
	pageTitle="Azure Insights : Utiliser les journaux d’audit pour envoyer des notifications webhook et par courrier électronique dans Azure Insights | Microsoft Azure"
	description="Découvrez comment utiliser les entrées des journaux d’audit pour appeler des URL web ou envoyer des notifications par courrier électronique dans Azure Insights. "
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring"
	documentationCenter="monitoring"/>

<tags
	ms.service="monitoring"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Utiliser les journaux d’audit pour envoyer des notifications webhook et par courrier électronique dans Azure Insights

Cet article vous montre le schéma de la charge utile lorsqu’un événement de journal d’audit déclenche un webhook. Il décrit aussi comment vous pouvez envoyer des courriers électroniques à l’aide de ce même événement.

>[AZURE.NOTE] Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Dans les prochains mois, l’infrastructure et les performances des alertes sur les événements et les performances seront améliorées. Dans cette version préliminaire, cette fonctionnalité est uniquement accessible avec Azure PowerShell et l’interface CLI. L’accès à cette fonctionnalité à partir du portail Azure sera disponible ultérieurement.

## Webhooks
Les webhooks vous permettent d’acheminer les notifications d’alerte Azure vers d’autres systèmes afin qu’elles soient traitées ou personnalisées. À titre d’exemple, citons l’acheminement de l’alerte vers des services qui peuvent gérer une demande web entrante pour envoyer des SMS, consigner des bogues ou informer un utilisateur par le biais de services de conversation ou de messagerie. L’URI du webhook doit être un point de terminaison HTTP ou HTTPS valide.

## Email
Les courriers électroniques peuvent être envoyés à n’importe quelle adresse électronique valide. Les administrateurs et les coadministrateurs de l’abonnement dans lequel la règle est exécutée seront également avertis.

### Exemple de règle de courrier électronique
Vous devez définir une règle de courrier électronique, une règle webhook, puis demander ensuite aux règles de démarrer lorsque l’événement du journal d’audit se produit. Vous pouvez voir un exemple d’utilisation de PowerShell dans [Exemples de démarrage rapide Azure Insights PowerShell](insights-powershell-samples.md#alert-on-audit-log-event).


## Authentification
Il existe deux formes d’URI d’authentification :

1. L’authentification par jeton en enregistrant l’URI du webhook avec un ID de jeton comme paramètre de requête. Par exemple, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. L’authentification de base avec un ID utilisateur et un mot de passe. Par exemple, https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## Schéma de la charge utile du webhook de notification d’événements dans le journal d’audit
Lorsqu’un nouvel événement est disponible, l’alerte sur les événements du journal d’audit exécute le webhook configuré avec les métadonnées d’événements dans la charge utile du webhook. L’exemple suivant montre le schéma de la charge utile du webhook :

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Nom de l’élément|	Description|
|---|---|
|status |Toujours défini sur « activated »|
|context|Contexte de l’événement|
|resourceProviderName|Fournisseur de la ressource affectée|
|conditionType |« Event »|
|name |nom de la règle d’alerte.|
|id |ID de ressource de l’alerte|
|description|	Description définie dans l’alerte par le créateur de l’alerte|
|subscriptionId |GUID d’abonnement Azure|
|timestamp|	Horodatage lorsque l’événement a été généré par le service Azure qui a traité la demande correspondant à l’événement|
|resourceId |URI d’ID de ressource qui identifie la ressource de façon unique|
|nom\_groupe\_ressources|Nom du groupe de ressources de la ressource affectée|
|properties |Ensemble de paires <Clé, Valeur> (c’est-à-dire Dictionary<String, String>) incluant des détails sur l’événement|
|event|Élément qui contient des métadonnées relatives à l’événement|
|autorisation|Capture les propriétés RBAC de l’événement. Il s’agit généralement de « action », « role » et « scope ».|
|category | Catégorie de l’événement. Les valeurs prises en charge sont : Administrative, Alert, Security, ServiceHealth, Recommendation|
|caller|Adresse électronique de l’utilisateur qui a effectué l’opération, la revendication UPN ou la revendication SPN basée sur la disponibilité. Peut être null pour certains appels système.|
|correlationId|	Généralement un GUID au format chaîne. Les événements avec correlationId appartiennent à la même action et partagent généralement le même correlationId.|
|eventDescription |Texte statique décrivant un événement|
|eventDataId|Identificateur unique d’un élément|
|eventSource |Nom de l’infrastructure ou du service Azure qui a généré l’événement|
|httpRequest|	Inclut généralement clientRequestId, clientIpAddress et la méthode (méthode HTTP PUT, par exemple).|
|level|Une des valeurs suivantes : Critical, Error, Warning, Informational et Verbose|
|operationId|Généralement, un GUID partagé par les événements correspondant à une opération unique|
|operationName|Nom de l’opération|
|properties |L’élément dans l’élément d’événement contient les propriétés de l’événement.|
|status|Chaîne décrivant l’état de l’opération. Les valeurs courantes sont : Started, In Progress, Succeeded, Failed, Active, Resolved|
|subStatus|	Inclut généralement le code d’état HTTP de l’appel REST correspondant. Il peut également inclure d’autres chaînes décrivant un sous-état. Les valeurs courantes sont : OK (Code d’état HTTP : 200), Created (Code d’état HTTP : 201), Accepted (Code d’état HTTP : 202), No content (Code d’état HTTP : 204), Bad Request (Code d’état HTTP : 400), Not found (Code d’état HTTP : 404), Conflict (Code d’état HTTP : 409), Internal Server Error (Code d’état HTTP : 500), Service unavailable (Code d’état HTTP : 503), Gateway Timeout (Code d’état HTTP : 504)|

<!---HONumber=AcomDC_0803_2016-->