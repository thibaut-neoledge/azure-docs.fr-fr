<properties 
   pageTitle="Continuité des activités de la base de données SQL au cours de la mise à niveau de l'application" 
   description="Cette section fournit de l’aide pour éviter les temps d’arrêt pendant une mise à niveau de l’application." 
   services="sql-database"
   documentationCenter="" 
   authors="elfisher" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/27/2016"
   ms.author="elfish"/>

#Mettre à niveau une application sans temps d’arrêt

Dans le contexte de Microsoft Azure, le terme « application » fait référence à des composants tels que les serveurs frontaux, les services déployés dans un service cloud et le niveau de données utilisé pour conserver les données ou les métadonnées d'application. Les applications cloud sont souvent conçues pour fournir un service ininterrompu 24h/24 et 7j/7. Le déploiement d'une nouvelle version de l'application lorsque les modifications dans le niveau de données sont appliquées sur le site actif peut provoquer des interruptions, comme la réduction des fonctionnalités disponibles ou même une interruption de service complète.

Lors de la conception du processus de mise à niveau de l'application, l'objectif principal doit être d'éliminer ou de réduire considérablement la durée pendant laquelle la fonctionnalité de l'application est réduite. Pour ce faire, le processus implique généralement la création d'une copie temporaire de l'application à utiliser comme sauvegarde en cas d'échec de la mise à niveau. Les facteurs suivants doivent être pris en compte lors de la conception et la planification de la mise à niveau :

1.	durée maximale acceptable pendant laquelle la fonctionnalité de l'application sera réduite ; 
2.	jeu minimal de fonctionnalités disponibles pendant le processus de mise à niveau ;
3.	possibilité de restauration en cas d'erreur pendant la mise à niveau.
4.	Coût total impliqué Ceci inclut le coût des composants d’application supplémentaires nécessaires pour créer une copie temporaire (par exemple, les bases de données supplémentaires pour la géoréplication active) et les coûts incrémentiels des déploiements temporaires utilisés par le processus de mise à niveau. 

Si l'application peut fonctionner temporairement en lecture seule, le workflow de mise à niveau peut être conçu de façon à éliminer entièrement les interruptions de service. Pour comprendre comment mettre en œuvre le workflow de mise à niveau pour la topologie de votre application spécifique, consultez [Gestion des mises à niveau propagées des applications cloud à l’aide de la géo-réplication active de la base de données SQL](sql-database-manage-application-rolling-upgrade.md).
 
 
 

<!---HONumber=AcomDC_0608_2016-->