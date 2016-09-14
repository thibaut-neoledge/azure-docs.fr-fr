<properties
   pageTitle="Vue d’ensemble d’Azure Resource Health | Microsoft Azure"
   description="Vue d’ensemble d’Azure Resource Health"
   services="Resource health"
   documentationCenter="dev-center-name"
   authors="BernardoAMunoz"
   manager=""
   editor=""/>

<tags
   ms.service="resource-health"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Supportability"
   ms.date="06/01/2016"
   ms.author="BernardoAMunoz"/>

# Vue d’ensemble d’Azure Resource Health

Azure Resource Health est un service qui expose l’intégrité des ressources Azure individuelles et fournit des conseils applicables permettant de résoudre les problèmes. Dans un environnement de cloud qui rend impossible l’accès direct aux serveurs ou aux éléments d’infrastructure, l’objectif de Resource Health est de réduire le temps que les clients consacrent au dépannage, notamment en réduisant la durée nécessaire pour déterminer si la racine du problème est interne à l’application ou si elle est liée à un événement au sein de la plateforme Azure.

## Qu’est-ce qu’une ressource et comment Resource Health juge-t-il de l’intégrité des ressources ? 
Une ressource correspond à une instance créée par un utilisateur d’un type de ressource fourni par un service, par exemple une machine virtuelle, une application web ou une base de données SQL.

Resource Health s’appuie sur des signaux émis par la ressource et/ou le service pour évaluer l’intégrité d’une ressource. Il est important de noter qu’actuellement, Resource Health se limite à l’intégrité d’un type de ressource spécifique et ne tient pas compte des autres éléments qui peuvent affecter l’intégrité globale. Par exemple, lors du signalement de l’état d’une machine virtuelle, seule la partie du calcul de l’infrastructure est prise en compte, ce qui signifie que les problèmes sur le réseau n’apparaissent pas dans Resource Health, sauf en cas d’interruption de service déclarée, auquel cas ils seront visibles dans la bannière en haut du panneau. Vous trouverez davantage d’informations sur les interruptions de service plus loin dans cet article.

## En quoi Resource Health est-il différent du tableau de bord d’état du service ?

Les informations fournies par Resource Health sont plus précises que celles du tableau de bord d’état du service. Tandis que le tableau de bord d’état du service indique des événements ayant un impact sur la disponibilité d’un service dans une région, Resource Health fournit des informations relatives à une ressource spécifique, notamment en signalant les événements ayant un impact sur la disponibilité d’une machine virtuelle, d’une application web ou d’une base de données SQL. Par exemple, si un nœud redémarre inopinément, les clients dont les machines virtuelles étaient exécutées sur ce nœud seront en mesure de savoir pourquoi leur machine virtuelle était indisponible pendant une période donnée.

## Accès à Resource Health
Pour les services disponibles via Resource Health, deux méthodes permettent d’accéder à Resource Health.

### Portail Azure
Le panneau Resource Health du portail Azure fournit des informations détaillées sur l’intégrité de la ressource ainsi que les actions recommandées, qui varient en fonction de l’état actuel de la ressource. Ce panneau offre la meilleure expérience possible lors de l’interrogation de Resource Health, dans la mesure où il facilite l’accès à d’autres ressources dans le portail. Comme mentionné précédemment, les actions recommandées dans le panneau Resource Health varient en fonction de l’état actuel :

* Ressources saines : dans la mesure où aucun problème susceptible d’affecter l’intégrité de la ressource n’a été détecté, les actions visent à aider le processus de dépannage. Par exemple, elles fournissent un accès direct au panneau Dépannage, qui propose des conseils sur la résolution des problèmes auxquels les clients sont le plus souvent confrontés.
* Ressources défectueuses : pour les problèmes causés par Azure, le panneau affiche les actions que Microsoft effectue (ou a effectué) pour récupérer la ressource. Pour les problèmes provoqués par des actions initiées par l’utilisateur, le panneau répertorie les actions que les clients peuvent effectuer pour résoudre le problème et récupérer la ressource.

Une fois que vous êtes connecté au portail Azure, vous pouvez accéder au panneau Resource Health de deux manières :

###Ouvrir le panneau Ressource
Ouvrez le panneau Ressource pour une ressource donnée. Dans le panneau Paramètres qui s’affiche en regard du panneau Ressource, cliquez sur Resource Health pour ouvrir le panneau Resource Health.

![Panneau Resource Health](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### Panneau Aide et support
Cliquez sur le point d’interrogation dans le coin supérieur droit, puis sélectionnez Aide + Support pour ouvrir le panneau Aide et support.

**Dans la barre de navigation supérieure**

![Aide + Support](./media/resource-health-overview/HelpAndSupport.png)

Si vous cliquez sur la mosaïque, le panneau d’abonnement à Resource Health s’ouvre. Il répertorie toutes les ressources de votre abonnement. Une icône située en regard de chaque ressource indique son état d’intégrité. Cliquez sur chaque ressource pour ouvrir le panneau Resource Health.

**Vignette Resource Health**

![Vignette Resource Health](./media/resource-health-overview/resourceHealthTile.png)

## Que signifie mon état Resource Health ?
Quatre états d’intégrité peuvent s’afficher pour votre ressource.

### Disponible
Le service n’a détecté aucun problème pouvant avoir un impact sur la disponibilité de la ressource. Cet état est représenté par une coche verte.

![La ressource est disponible](./media/resource-health-overview/Available.png)

### Non disponible

Dans ce cas, le service a détecté un problème qui affecte la disponibilité de cette ressource, par exemple, le nœud sur lequel la machine virtuelle était exécutée a redémarré de manière inattendue. Cet état est représenté par une icône d’avertissement rouge. Des informations supplémentaires sur le problème sont fournies dans la section située au milieu du panneau, notamment :

1.	Quelles sont les actions exécutées par Microsoft pour récupérer la ressource
2.	Une chronologie détaillée du problème, y compris le délai de résolution prévu
3.	Une liste des actions recommandées pour les utilisateurs

![La ressource est indisponible](./media/resource-health-overview/Unavailable.png)

### Non disponible – initié par le client
La ressource n’est pas disponible en raison d’une requête client, comme l’arrêt d’une ressource ou un redémarrage. Cet état est représenté par une icône d’information bleue.

![La ressource est indisponible en raison d’une action initiée par l’utilisateur](./media/resource-health-overview/userInitiated.png)

### Unknown
Le service n’a reçu aucune information sur cette ressource depuis plus de 5 minutes. Cet état est représenté par un point d’interrogation vert.

Veuillez noter qu’il ne s’agit pas d’une indication précise qu’un problème touche une ressource. Les clients doivent donc suivre les recommandations suivantes :

* Si la ressource s’exécute comme prévu mais que son état d’intégrité est inconnu dans Resource Health, il n’y a aucun problème et vous pouvez vous attendre à ce que l’état de la ressource soit mis à jour après quelques minutes pour indiquer que la ressource est saine.
* Si vous rencontrez des problèmes pour accéder à la ressource et que son état est inconnu dans Resource Health, cela peut être un premier signe indiquant la présence d’un problème. Dans ce cas, des recherches supplémentaires doivent être effectuées jusqu'à ce que l’état soit mis à jour pour indiquer l’intégrité ou la défaillance de la ressource.

![L’intégrité de la ressource est inconnue](./media/resource-health-overview/unknown.png)

## Événements ayant un impact sur le service
Si la ressource est susceptible d’être affectée par un événement ayant un impact sur le service, une bannière s’affiche en haut du panneau Resource Health. Si vous cliquez sur la bannière, le panneau Événements d’audit s’ouvre et fournit des informations supplémentaires sur la panne.

![L’intégrité de la ressource peut être affectée par un événement ayant un impact sur le service](./media/resource-health-overview/serviceImpactingEvent.png)

## Que dois-je savoir sur Resource Health ?

### Latence de signal
Les signaux qui alimentent Resource Health peuvent accuser un retard allant jusqu’à 15 minutes, ce qui peut entraîner des différences entre l’état d’intégrité actuel de la ressource et sa disponibilité réelle. Il est important de garder cette information à l’esprit, car elle permet d’éviter de passer inutilement du temps à rechercher les problèmes possibles.

### Cas spécial pour SQL 
Resource Health indique l’état de la base de données SQL, pas du serveur SQL. Bien que cette méthode fournisse une image plus réaliste de l’intégrité, elle requiert que plusieurs composants et services soient pris en compte pour déterminer l’intégrité de la base de données. Le signal actuel repose sur les connexions à la base de données, ce qui signifie que pour les bases de données qui reçoivent des connexions régulières (y compris des demandes d’exécution de requête), l’état d’intégrité sera régulièrement affiché. Si la base de données n’a pas été utilisée pendant au moins 10 minutes, son état sera considéré comme inconnu. Cela ne signifie pas que la base de données est indisponible, mais simplement qu’aucun signal n’a été émis car aucune connexion n’a eu lieu. La connexion à la base de données et l’exécution d’une requête émettront les signaux nécessaires pour déterminer et mettre à jour l’état d’intégrité de la base de données.

## Commentaires
Nous sommes ouverts aux commentaires et suggestions ! Envoyez-nous vos [suggestions](https://feedback.azure.com/forums/266794-support-feedback). Vous pouvez aussi nous contacter via [Twitter](https://twitter.com/azuresupport) ou via les [forums MSDN](https://social.msdn.microsoft.com/Forums/azure).

<!---HONumber=AcomDC_0831_2016-->