<properties 
   pageTitle="Restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure utilisant l'API REST" 
   description="restauration jusqu'à une date et heure, base de données SQL Microsoft Azure, restaurer une base de données, récupérer une base de données, API REST" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal"/>

# Restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure utilisant l'API REST

> [AZURE.SELECTOR]
- [Restauration jusqu'à une date et heure - Portail](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)
- [Restauration jusqu'à une date et heure - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-powershell/)

## Présentation

Ce didacticiel vous montre comment restaurer une base de données SQL Azure à l'aide de la restauration jusqu'à une date et heure utilisant l'API REST. Il fournit également des liens vers des procédures plus détaillées.

La restauration jusqu'à une date et heure entraîne la création d'une nouvelle base de données. Le service sélectionne automatiquement le niveau de service en fonction de la sauvegarde utilisée au point de restauration. Assurez-vous que le quota disponible sur le serveur logique est suffisant pour permettre la création d'une autre base de données. Si vous avez besoin d'un quota supérieur, contactez le [support technique d'Azure](http://azure.microsoft.com/support/options/).

## Restrictions et sécurité

Consultez [Restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure dans le portail Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/).

## Procédure : restauration d'une base de données SQL Azure en utilisant l'API REST

1.	Obtenez la base de données à restaurer en suivant la procédure [Obtention de base de données](http://msdn.microsoft.com/library/azure/dn505708.aspx).

2.	Créez la demande de restauration en suivant la procédure [Création d'une demande de restauration de base de données](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Suivez l'état de la restauration en suivant la procédure [Statut de l'opération de base de données](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Étapes suivantes

Pour plus d'informations, consultez les liens suivants : 

[Continuité des activités de l'entreprise dans la base de données SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sauvegarde et restauration de base de données SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Restauration jusqu'à une date et heure d'une base de données SQL Azure (blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Référence de l'API REST de gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx)

<!--HONumber=47-->
