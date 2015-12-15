<properties
 pageTitle="Concepts, termes et entités du planificateur | Microsoft Azure"
 description="Concepts, terminologie et hiérarchie des entités d’Azure Scheduler, notamment les travaux et les collections de travaux. Fournit un exemple complet d’un exemple de tâche planifiée."
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# Concepts, terminologie et hiérarchie d’entités de Scheduler

## Hiérarchie d’entités de Scheduler

Le tableau suivant décrit les ressources principales exposées ou utilisées par l'API de Scheduler :

|Ressource | Description |
|---|---|
|**Service cloud**|Sur le plan conceptuel, un service cloud représente une application. Un abonnement peut avoir plusieurs services cloud.|
|**Collection de travaux**|Une collection de travaux contient un groupe de travaux et conserve les paramètres, les quotas et les limitations qui sont partagés par les travaux au sein de la collection. Une collection de travaux est créée par le propriétaire d’un abonnement et regroupe des travaux en fonction des limites de l’utilisation ou de l’application. Une collection est limitée à une région. Elle permet également la mise en œuvre de quotas pour limiter l’utilisation de tous les travaux de la collection. Les quotas incluent MaxJobs et MaxRecurrence.|
|**Travail**|Un travail définit une seule action récurrente, avec des stratégies d'exécution simples ou complexes. Les actions peuvent inclure des requêtes HTTP ou des requêtes de file d'attente de stockage.|
|**Historique des travaux**|Un historique des travaux représente les détails de l'exécution d'un travail. Il contient le succès ou l'échec, ainsi que les détails de la réponse.|

## Gestion des entités de Scheduler

Globalement, le planificateur et l'API de gestion de service exposent les opérations suivantes sur les ressources :

|Fonctionnalité|Description et adresse URI|
|---|---|
|**Gestion du service cloud**|Prise en charge de GET, PUT et DELETE pour la création et la modification des services cloud <p>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}`</p>|
|**Gestion de la collection de travaux**|Prise en charge de GET, PUT et DELETE pour la création et la modification des collections de travaux et des travaux qu'elles contiennent. Une collection de travaux sert de conteneur pour les travaux et mappe ceux-ci aux quotas et paramètres partagés. Les exemples de quotas présentés ultérieurement, sont le nombre maximal de travaux et le plus petit intervalle de périodicité. <p>PUT et DELETE : `https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/jobcollections/{jobCollectionName}`</p><p>GET : `https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}`</p>
|**Gestion des travaux**|Prise en charge de GET, PUT, POST, PATCH et DELETE pour la création et la modification des travaux. Tous les travaux doivent appartenir à une collection de travaux qui existe déjà, afin qu’il n’y ait pas de création implicite. <p>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}/jobs/{jobId}`</p>|
|**Gestion de l'historique des travaux**|Prise en charge de GET pour l'extraction de 60 jours d'historique d'exécution, comme le temps de travail écoulé et les résultats d'exécution du travail. Ajoute la prise en charge du paramètre de chaîne de requête pour le filtrage basé sur l’état et le statut. <P>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}/jobs/{jobId}/history`</p>|

## Types de travaux

Il existe deux types de travaux : les travaux HTTP (y compris les travaux HTTPS qui prennent en charge SSL) et les travaux de file d'attente de stockage. Les travaux HTTP sont idéaux si vous disposez d'un point de terminaison d'une charge de travail ou d'un service existant. Vous pouvez utiliser les travaux de file d’attente de stockage pour publier des messages aux files d’attente de stockage et donc, ces travaux sont idéaux pour les charges de travail qui utilisent des files d’attente de stockage.

## L’entité « travail » en détail

À un niveau de base, un travail planifié comporte plusieurs éléments :

- L'action à effectuer lorsque le travail se déclenche  

- (Facultatif) L'heure d'exécution du travail

- (Facultatif) Quand et à quelle fréquence répéter le travail

- (Facultatif) Une action à déclencher en cas d'échec de l'action principale

En interne, un travail planifié contient également des données fournies par le système, comme l'heure d'exécution planifiée suivante.

Le code suivant fournit un exemple complet d’un exemple de tâche planifiée. Les détails sont fournis dans les sections suivantes.

	{
		"startTime": "2012-08-04T00:00Z",               // optional
		"action":
		{
			"type": "http",
			"retryPolicy": { "retryType":"none" },
			"request":
			{
				"uri": "http://contoso.com/foo",        // required
				"method": "PUT",                        // required
				"body": "Posting from a timer",         // optional
				"headers":                              // optional

				{
					"Content-Type": "application/json"
				},
			},
		   "errorAction":
		   {
			   "type": "http",
			   "request":
			   {
				   "uri": "http://contoso.com/notifyError",
				   "method": "POST",
			   },
		   },
		},
		"recurrence":                                   // optional
		{
			"frequency": "week",                        // can be "year" "month" "day" "week" "minute"
			"interval": 1,                              // optional, how often to fire (default to 1)
			"schedule":                                 // optional (advanced scheduling specifics)
			{
				"weekDays": ["monday", "wednesday", "friday"],
				"hours": [10, 22]
			},
			"count": 10,                                 // optional (default to recur infinitely)
			"endTime": "2012-11-04",                     // optional (default to recur infinitely)
		},
		"state": "disabled",                           // enabled or disabled
		"status":                                       // controlled by Scheduler service
		{
			"lastExecutionTime": "2007-03-01T13:00:00Z",
			"nextExecutionTime": "2007-03-01T14:00:00Z ",
			"executionCount": 3,
											    "failureCount": 0,
												"faultedCount": 0
		},
	}

Comme indiqué dans l’exemple de travail de Scheduler ci-dessus, une définition de travail comporte plusieurs éléments :

- Heure de début (« startTime »)  

- Action (« action »), qui inclut l'action d'erreur (« errorAction »)

- Récurrence (« recurrence »)

- État (« state »)

- Statut (« status »)

- Stratégie de nouvelle tentative (« retryPolicy »)

Examinons chacun en détail :

## startTime

« startTime » correspond à l’heure de début et permet à l’appelant de spécifier un décalage de fuseau horaire sur le câble au [format ISO-8601](http://en.wikipedia.org/wiki/ISO_8601).

## action et errorAction

« action » est l’action appelée sur chaque occurrence et décrit un type d’appel de service. L’action correspond à l’opération qui sera exécutée, en fonction de la planification spécifiée. Scheduler prend en charge les actions HTTP et file d’attente de stockage.

L’action dans l’exemple ci-dessus est une action http. Voici un exemple d'action de file d'attente de stockage :

	{
			"type": "storageQueue",
			"queueMessage":
			{
				"storageAccount": "myStorageAccount",  // required
				"queueName": "myqueue",                // required
				"sasToken": "TOKEN",                   // required
				"message":                             // required
					"My message body",
			},
	}

« errorAction » est le gestionnaire d'erreurs, l'action appelée lorsque l'action principale échoue. Vous pouvez utiliser cette variable pour appeler un point de terminaison de gestion d’erreur ou envoyer une notification utilisateur. L’opération peut servir à atteindre un point de terminaison secondaire au cas où le premier ne serait pas disponible (par exemple, en cas de sinistre sur le site du point de terminaison) ou pour notifier un point de terminaison de traitement d’erreur. Comme l'action principale, l'action d'erreur peut être une logique simple ou composite basée sur d'autres actions. Pour savoir comment créer un jeton SAS, consultez [Créer et utiliser une signature d'accès partagé](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## recurrence

La récurrence comporte plusieurs parties :

- La fréquence (frequency) : minute, heure, jour, semaine, mois, année  

- L'intervalle (interval) : intervalle à la fréquence donnée pour la récurrence

- La planification prescrite (prescribed schedule) : spécifiez les minutes, heures, jours de la semaine, mois et jours du mois de la récurrence

- Le nombre (count) : nombre d'occurrences

- L'heure de fin (end time) : aucun travail ne s'exécutera après l'heure de fin spécifiée

Un travail est récurrent s'il comporte un objet récurrent spécifié dans sa définition JSON. Si les valeurs count et endTime sont toutes deux spécifiées, la règle d'achèvement qui se produit en premier est honorée.

## state

L'état du travail a l'une de quatre valeurs : activé, désactivé, terminé ou a généré une erreur. Vous pouvez exécuter PUT ou PATCH sur les travaux afin de les mettre à jour sur l'état activé ou désactivé. Si un travail a été terminé ou a généré une erreur, cet état final ne peut pas être mis à jour (bien que le travail puisse encore être supprimé). Vous trouverez ci-dessous un exemple de la propriété state :


    	"state": "disabled", // enabled, disabled, completed, or faulted
Les travaux terminés et ayant généré une erreur sont supprimés après 60 jours.

## status

Lorsqu'un travail de Scheduler a démarré, des informations sur l'état actuel du travail sont renvoyées. Cet objet n’est pas définissable par l’utilisateur ; il est défini par le système. Toutefois, il est inclus dans l'objet du travail (plutôt qu'en tant que ressource liée distincte) afin que l'utilisateur puisse obtenir l'état d'un travail facilement.

L'état du travail inclut l'heure de l'exécution précédente (le cas échéant), l'heure de la prochaine exécution planifiée (pour les travaux en cours) et le nombre d'exécutions du travail.

## retryPolicy

En cas d'échec d'un travail de Scheduler, il est possible de spécifier une stratégie de nouvelle tentative pour déterminer si et comment l'action est retentée. Ceci est déterminé par l’objet **retryType**. Il est défini sur **none** s’il n’existe aucune stratégie de nouvelle tentative, comme indiqué ci-dessus. Définissez-le sur **fixed** s'il existe une stratégie de nouvelle tentative.

Pour définir une stratégie de nouvelle tentative, deux paramètres supplémentaires peuvent être spécifiés : un intervalle de nouvelle tentative (**retryInterval**) et le nombre de nouvelles tentatives (**retryCount**).

L’intervalle de nouvelle tentative, spécifié avec l’objet **retryInterval**, est l’intervalle entre les nouvelles tentatives. Sa valeur par défaut est de 1 minute, sa valeur minimale est de 1 minute et sa valeur maximale est de 18 mois. Il est défini dans le format ISO-8601. De même, la valeur du nombre de nouvelles tentatives est spécifiée avec l'objet **retryCount**. Il s'agit du nombre de nouvelles tentatives. Sa valeur par défaut est de 5 et sa valeur maximale est de 20. Les deux **retryInterval** et **retryCount** sont facultatifs. Ils reçoivent leur valeur par défaut si **retryType** est défini sur **fixed** et si aucune valeur n’est spécifiée explicitement.

## Voir aussi

 [Présentation d'Azure Scheduler](scheduler-intro.md)
 
 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Comment créer des planifications complexes et une périodicité avancée avec Azure Scheluler](scheduler-advanced-complexity.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/dn528946)

 [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d’erreur d’Azure Scheluler](scheduler-limits-defaults-errors.md)

 [Authentification sortante d’Azure Scheluler](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_1210_2015-->