<properties
 pageTitle="Haute disponibilité et fiabilité de Scheduler"
 description="Haute disponibilité et fiabilité de Scheduler"
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


# Haute disponibilité et fiabilité de Scheduler

## Haute disponibilité d'Azure Scheduler

En tant que service central de la plateforme Microsoft Azure, Azure Scheduler est hautement disponible et propose le déploiement de service géo-redondant et la réplication de travail géo-régionale.

### Déploiement de service géo-redondant

Azure Scheduler est disponible par le biais de l'interface utilisateur dans presque chaque région géographique incluse dans Azure actuellement. La liste des régions dans lesquelles Azure Scheduler est disponible est [répertoriée ici](https://azure.microsoft.com/regions/#services). Si un centre de données dans une région hébergé devient indisponible, les fonctionnalités de basculement d'Azure Scheduler sont telles que le service est disponible à partir d'un autre centre de données.

### Réplication de travail géo-régionale

Le composant frontal Azure Scheduler est non seulement disponible pour les requêtes de gestion, mais votre propre travail est également répliqué au niveau géographique. En cas de panne dans une région, Azure Scheduler bascule et garantit que le travail est exécuté à partir d'un autre centre de données dans la région géographique associée.

Par exemple, si vous avez créé un travail dans le Centre-Sud des États-Unis, Azure Scheduler réplique automatiquement ce travail dans le Centre-Nord des États-Unis. En cas de défaillance dans le Centre-Sud des États-Unis, Azure Scheduler s'assure que le travail est exécuté depuis le Centre-Nord des États-Unis.

![][1]

Par conséquent, Azure Scheduler garantit que vos données restent dans la même région géographique plus large en cas de défaillance d'Azure. Donc, vous n'avez pas besoin de dupliquer votre travail uniquement pour ajouter une haute disponibilité. Azure Scheduler fournit automatiquement des fonctionnalités de haute disponibilité pour vos travaux.

## Fiabilité d'Azure Scheduler

Azure Scheduler garantit sa propre haute disponibilité et adopte une approche différente pour les travaux créés par l'utilisateur. Par exemple, votre travail peut appeler un point de terminaison HTTP qui n'est pas disponible. Azure Scheduler tente néanmoins d'exécuter votre travail avec succès, en vous proposant des options alternatives pour gérer les défaillances. Azure Scheduler effectue cette opération de deux manières :

### Stratégie de nouvelle tentative configurable avec « retryPolicy »

Azure Scheduler vous permet de configurer une stratégie de nouvelle tentative. Par défaut, si un travail échoue, Azure Scheduler tente d'exécuter le travail encore quatre fois, à des intervalles de 30 secondes. Vous pouvez reconfigurer cette stratégie de nouvelle tentative pour qu'elle soit plus agressive (par exemple, dix fois, à des intervalles de 30 secondes) ou plus souple (par exemple, deux fois, à des intervalles quotidiens).

Pour illustrer quand cette fonction peut s'avérer utile, vous pouvez créer un travail qui s'exécute une fois par semaine et appelle un point de terminaison HTTP. Si le point de terminaison HTTP est à l'arrêt pendant quelques heures lorsque votre travail s'exécute, il est possible que vous ne vouliez pas attendre une semaine de plus pour réexécuter le travail dans la mesure où même la stratégie de nouvelle tentative par défaut échouera. Dans ce cas, vous pouvez reconfigurer la stratégie de nouvelle tentative standard pour réessayer toutes les trois heures (par exemple) au lieu de toutes les 30 secondes.

Pour savoir comment configurer une stratégie de nouvelle tentative, consultez [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### Capacité de configuration du point de terminaison alternatif avec « errorAction »

Si le point de terminaison cible pour votre travail Azure Scheduler reste inaccessible, Azure Scheduler revient au point de terminaison alternatif de traitement des erreurs après avoir appliqué sa stratégie de nouvelle tentative. Si un point de terminaison alternatif de traitement des erreurs est configuré, Azure Scheduler l'appelle. Avec un point de terminaison alternatif, vos propres travaux sont hautement disponibles en cas de défaillance.

Par exemple, dans le diagramme ci-dessous, Azure Scheduler suit sa stratégie de nouvelle tentative pour atteindre un service Web de New York. Lorsque les nouvelles tentatives échouent, il vérifie s'il existe une alternative. Ensuite, il commence à effectuer des requêtes auprès du point de terminaison alternatif avec la même stratégie de nouvelle tentative.

![][2]

Notez que la même stratégie de nouvelle tentative s'applique à l'action d'origine et à l'action d'erreur alternative. Le type d'action de l'action d'erreur alternative peut également être différent du type d'action de l'action principale. Par exemple, tandis que l’action principale peut appeler un point de terminaison HTTP, l’action d’erreur peut à la place être une action de file d’attente de stockage, de file d’attente Service Bus ou de rubrique Service Bus qui effectue la journalisation des erreurs.

Pour savoir comment configurer un point de terminaison alternatif, consultez [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## Voir aussi

 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Comment créer des planifications complexes et une périodicité avancée avec Azure Scheluler](scheduler-advanced-complexity.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/mt629143)

 [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)

 [Limites, valeurs par défaut et codes d’erreur d’Azure Scheluler](scheduler-limits-defaults-errors.md)

 [Authentification sortante d’Azure Scheluler](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png

<!---HONumber=AcomDC_0420_2016-->