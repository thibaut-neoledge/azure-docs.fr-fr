<properties 
   pageTitle="Restauration d’une base de données SQL Azure supprimée avec l’API REST" 
   description="base de données SQL Microsoft Azure, restaurer une base de données supprimée, récupérer une base de données supprimée, API REST" 
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
   ms.date="03/17/2015"
   ms.author="elfish; v-romcal"/>

# Restauration d’une base de données SQL Azure supprimée avec l’API REST

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md) 

## Vue d’ensemble

Ce didacticiel vous montre comment restaurer une base de données SQL Azure supprimée avec l’API REST. Il fournit également des liens vers des procédures plus détaillées.

La restauration d’une base de données SQL Azure supprimée entraîne la création d’une nouvelle base de données. Le service sélectionne automatiquement le niveau de service en fonction de la sauvegarde utilisée au point de restauration. Assurez-vous que le quota disponible sur le serveur logique est suffisant pour permettre la création d’une autre base de données. Si vous avez besoin d’un quota supérieur, contactez le [support technique Azure](http://azure.microsoft.com/support/options/).

## Restrictions et sécurité

Consultez [Restauration d’une base de données SQL Azure supprimée dans le portail Azure](sql-database-restore-deleted-database-tutorial-management-portal.md).

## Procédure : restauration d’une base de données SQL Azure supprimée avec l’API REST

1.	Obtenez la liste de toutes vos bases de données supprimées pouvant être restaurées en suivant la procédure de création de [listes des bases de données supprimées pouvant être restaurées](http://msdn.microsoft.com/library/azure/dn509562.aspx).
	
2.	Obtenez des informations détaillées sur la base de données que vous voulez restaurer en suivant la procédure d’[obtention de base de données supprimée pouvant être restaurée](http://msdn.microsoft.com/library/azure/dn509574.aspx).

3.	Lancez la restauration en suivant la procédure de [création d’une demande de restauration de base de données](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
4.	Surveillez l’état de la restauration en suivant la procédure de suivi de l’[état de fonctionnement de base de données](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Étapes suivantes

Pour plus d’informations, consultez les liens suivants :

[Continuité de l’activité des bases de données SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sauvegarde et restauration de base de données SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Référence de l’API REST de gestion des services](http://msdn.microsoft.com/library/azure/ee460799.aspx)

<!---HONumber=62-->