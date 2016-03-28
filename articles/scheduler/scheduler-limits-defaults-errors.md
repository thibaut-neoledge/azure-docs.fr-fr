<properties
 pageTitle="Limites, valeurs par défaut et codes d'erreur de Scheduler"
 description=""
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
 ms.topic="article"
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Limites, valeurs par défaut et codes d'erreur de Scheduler

## Limitations, valeurs par défaut, limites et quotas de Scheduler

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## L'en-tête x-ms-request-id

Chaque requête adressée au service de Scheduler retourne un en-tête de réponse nommé **x-ms-request-id**. Cet en-tête contient une valeur opaque qui identifie de façon unique la requête.

Si une requête échoue constamment et que vous avez vérifié qu'elle est formulée correctement, vous pouvez utiliser cette valeur pour signaler l'erreur à Microsoft. Dans votre rapport, incluez la valeur de x-ms-request-id, l'heure approximative de la requête, l'identificateur de l'abonnement, le service cloud, la collection de travaux et/ou le travail et le type d'opération tentée par la requête.

## Codes d'état et codes d'erreur de Scheduler

En plus des codes d'état HTTP standard, l'API REST Azure Scheduler retourne des codes d'erreur étendus et des messages d'erreur. Les codes étendus ne remplacent pas les codes d'état HTTP standard, mais fournissent des informations supplémentaires et exploitables qui peuvent être utilisées conjointement avec les codes d'état HTTP standard.

Par exemple, une erreur HTTP 404 peut se produire pour de nombreuses raisons. Disposer d'informations supplémentaires dans le message étendu peut aider à résoudre le problème. Pour plus d'informations sur les codes HTTP standard retournés par l'API REST, consultez [Codes d'état et codes d'erreur de la gestion de service](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx). Les opérations API REST de l'API de gestion des services retournent des codes d'état HTTP standard, tels que définis sur la page de [définition des codes d'état HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Le tableau suivant décrit les erreurs courantes qui peuvent être retournées par le service.

|Code d'erreur|Code d’état HTTP|Message utilisateur|
|----|----|----|
|MissingOrIncorrectVersionHeader|Requête incorrecte (400)|L'en-tête de contrôle de version n'est pas spécifié ou a été spécifié de manière incorrecte.|
|InvalidXmlRequest|Requête incorrecte (400)|Le code XML du corps de la requête n'était pas valide ou était spécifié incorrectement.|
|MissingOrInvalidRequiredQueryParameter|Requête incorrecte (400)|Un paramètre de requête requis n'a pas été spécifié pour cette requête ou a été spécifié de manière incorrecte.|
|InvalidHttpVerb|Requête incorrecte (400)|Le verbe HTTP spécifié n'a pas été reconnu par le serveur ou n'est pas valide pour cette ressource.|
|AuthenticationFailed|Interdit (403)|Le serveur n'a pas pu authentifier la requête. Vérifiez que le certificat est valide et qu'il est associé à cet abonnement.|
|ResourceNotFound|Introuvable (404)|La ressource spécifiée n'existe pas.|
|InternalError|Erreur interne du serveur (500)|une erreur interne s'est produite sur le serveur. Relancez la requête.|
|OperationTimedOut|Erreur interne du serveur (500)|Impossible d'exécuter l'opération dans le temps imparti.|
|ServerBusy|Service indisponible (503)|Le serveur (ou un composant interne) est actuellement indisponible pour recevoir des requêtes. Relancez la requête.|
|SubscriptionDisabled|Interdit (403)|L'abonnement a un état désactivé.|
|BadRequest|Requête incorrecte (400)|Un paramètre était incorrect.|
|ConflictError|Conflit (409)|Un conflit s'est produit pour empêcher l'opération de se terminer.|
|TemporaryRedirect|Redirection temporaire (307)|L'objet demandé n'est pas disponible. Une URI temporaire pour le nouvel emplacement de l'objet peut être obtenue à partir du champ Emplacement de la réponse. La requête d'origine peut être répétée sur la nouvelle URI.|

Les opérations API peuvent également retourner des informations d'erreur supplémentaires définies par le service de gestion. Ces informations d'erreur supplémentaires sont retournées dans le corps de la réponse.

## Voir aussi


 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/mt629143)

 [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)

 [Authentification sortante d’Azure Scheluler](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0316_2016-->