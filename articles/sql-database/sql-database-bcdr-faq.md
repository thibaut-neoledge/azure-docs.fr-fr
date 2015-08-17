<properties 
   pageTitle="FAQ sur la continuité des activités d'une base de données SQL Azure" 
   description="Questions courantes (et les réponses associées) posées par les clients sur les fonctionnalités intégrées et facultatives pour la continuité des activités et la récupération d'urgence avec une base de données SQL Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>


<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/14/2015"
   ms.author="elfish"/>


# FAQ sur la continuité des activités

## 1\. Que se passe-t-il pour la période de rétention du point de restauration lorsque je mets à niveau vers une version antérieure/supérieure par niveau de service ?
Après la mise à niveau vers un niveau de performances inférieur, la période de rétention du point de restauration est immédiatement tronquée en fonction de la période de rétention du niveau de performances de la base de données actuelle.

Si le niveau de service de la base de données est mis à niveau, la période de rétention s'étendra uniquement après la mise à niveau de la base de données.

Par exemple, si la base de données est mise à niveau de P1 vers S3, la période de rétention passera immédiatement de 35 jours à 14 jours. Tous les points de restauration antérieurs à 14 jours ne seront plus disponibles. Par la suite, si la base de données est remise à niveau vers P1, la période de rétention commencera à 14 jours et s'étendra jusqu'à 35 jours.

## 2\. Quelle est la durée d'une période de rétention pour une base de données supprimée ? 
La période de rétention est déterminée par le niveau de service de la base de données au moment de son existence ou le nombre de jours pendant laquelle la base de données existe, quel que soit le chiffre inférieur.

## 3\. Comment puis-je restaurer un serveur supprimé ?

Il n'existe aucune prise en charge pour la restauration d'un serveur supprimé pour l'instant.

## 4\. Combien de temps faut-il pour restaurer une base de données ?

La durée nécessaire pour restaurer une base de données dépend de plusieurs facteurs, tels que la taille de la base de données, le nombre de journaux de transactions, la bande passante réseau, etc. La majorité de la base de données est restaurée dans un délai de 12 heures.

## 5\. Puis-je modifier la période de rétention du point de restauration de ma base de données ?

Non.

## 6\. Comment trouver le point de restauration disponible pour ma base de données ?

Pour une récupération après une erreur utilisateur : l'heure actuelle est le dernier point de restauration disponible. Pour trouver le plus ancien point de restauration disponible, utilisez [Obtenir la base de données](https://msdn.microsoft.com/library/dn505708.aspx) (*RecoveryPeriodStartDate*) pour obtenir le plus ancien point de restauration (point de restauration sans réplication géographique).

Pour une récupération après panne : utilisez [Obtenir une base de données récupérable](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) pour obtenir le dernier point de restauration avec réplication géographique.

## 7\. Comment puis-je restaurer en bloc des bases de données sur mon serveur ?

Il n'existe aucune fonctionnalité intégrée pour une restauration en bloc. Vous pouvez utiliser le script [Base de données SQL Azure : récupération de serveur complète](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) pour accomplir cette tâche.

## 8\. Quelle est la différence entre la géo-réplication standard et la géo-réplication active ?

Pour la géo-réplication standard, la base de données secondaire n'est pas lisible. Elle est uniquement disponible pour le basculement en cas de panne.

Pour la géo-réplication active, toutes les bases de données secondaires sont lisibles (jusqu'à 4 BD secondaires).

## 9\. Quel est le délai de réplication lors de l'utilisation de la géo-réplication standard ou de la géo-réplication active ?

La géo-réplication utilise la copie continue. Par conséquent, utilisez la vue de gestion dynamique (DMV) [sys.dm\_continuous\_copy\_status](https://msdn.microsoft.com/library/azure/dn741329.aspx) pour obtenir la dernière réplication et d'autres informations.




 

<!---HONumber=August15_HO6-->