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
 ms.date="04/22/2015" 
 ms.author="krisragh"/>
 
# Limites, valeurs par défaut et codes d'erreur de Scheduler

## Limitations, valeurs par défaut, limites et quotas de Scheduler

Le tableau suivant décrit chaque quota, limite, valeur par défaut et limitation principaux dans Azure Scheduler.

|Ressource|Description de la limite|
|---|---|
|**Taille du travail**|La taille maximale du travail est de 16 Ko. Si une commande PUT ou PATCH génère un travail qui dépasse ces limites, un code d'état 400 demande incorrecte est retourné.|
|**Taille d'URL de la requête**|La taille maximale de l'URL de la requête est de 2 048 caractères.|
|**Taille de l'en-tête d'agrégat**|La taille maximale de l'en-tête d'agrégat est de 4 096 caractères.|
|**Nombre d'en-têtes**|Le nombre maximal d'en-têtes est 50.|
|**Taille du corps**|La taille maximale du corps est 8 192 caractères.|
|**Période de récurrence**|La période de récurrence maximale est 18 mois.|
|**Délai jusqu'à l'heure de début**|Le « délai jusqu'à l'heure de début » maximal est 18 mois.|
|**Historique des travaux**|Le corps de réponse maximal stocké dans l'historique des travaux est 2 048 octets.|
|**Fréquence**|Le quota de fréquence maximale par défaut est 1 heure dans une collection de travaux gratuite et 1 minute dans une collection de travaux standard. La fréquence maximale est configurable dans une collection de travaux pour être inférieure à la valeur maximale. Tous les travaux dans la collection de travaux sont limités la valeur définie dans la collection de travaux. Si vous essayez de créer un travail avec une fréquence supérieure à la fréquence maximale de la collection de travaux, la requête échoue avec un code d'état 409 conflit.|
|**Travaux**|Le quota de travaux maximum par défaut est 5 travaux dans une collection de travaux gratuite et 50 travaux dans une collection de travaux standard. Le nombre maximal de travaux est configurable dans une collection de travaux. Tous les travaux dans la collection de travaux sont limités la valeur définie dans la collection de travaux. Si vous essayez de créer plus de travaux que le quota maximal, la requête échoue avec un code d'état 409 conflit.|
|**Conservation de l'historique des travaux**|L'historique des travaux est conservé pendant 2 mois.|
|**Conservation des travaux terminés et ayant généré une erreur**|Les travaux terminés et ayant généré une erreur sont conservés pendant 60 jours.|
|**Délai d'expiration**|Il existe un délai d'expiration de requête (non modifiable) statique de 30 secondes pour les actions HTTP. Pour les opérations en cours d'exécution plus longues, suivez les protocoles HTTP asynchrones ; par exemple, retourner un 202 immédiatement, mais continuer de travailler en arrière-plan.|

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

Les opérations API peuvent également retourner des informations d'erreur supplémentaires définies par le service de gestion. Ces informations d'erreur supplémentaires sont retournées dans le corps de la réponse. Le corps de la réponse d'erreur suit le format de base ci-dessous.

		<?xml version="1.0" encoding="utf-8"?>  
		<Error>  
			<Code>string-code</Code>  
			<Message>detailed-error-message</Message>  
		</Error>  

## Voir aussi

 [Concepts, terminologie et hiérarchie d'entités de Scheduler](scheduler-concepts-terms.md)
 
 [Prise en main de Scheduler dans le portail de gestion](scheduler-get-started-portal.md)
 
 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)
 
 [Comment créer des planifications complexes et une périodicité avancée avec Azure Scheduler](scheduler-advanced-complexity.md)
 
 [Informations de référence sur l'API REST de Scheduler](https://msdn.microsoft.com/library/dn528946)
 
 [Haute disponibilité et fiabilité de Scheduler](scheduler-high-availability-reliability.md)
 
 [Authentification sortante de Scheduler](scheduler-outbound-authentication.md)

  

<!---HONumber=July15_HO4-->