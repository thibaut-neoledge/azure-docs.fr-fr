<properties 
   pageTitle="Récupérer une base de données SQL Azure à l’aide de la géo-restauration dans le portail Azure" 
   description="géo-restauration, base de données SQL Microsoft Azure, restaurer une base de données, récupérer une base de données, portail de gestion Azure, portail Azure" 
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
   ms.date="07/24/2015"
   ms.author="elfish; v-romcal"/>

# Récupérer une base de données SQL Azure à l’aide de la géo-restauration dans le portail Azure

> [AZURE.SELECTOR]
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)   

## Vue d’ensemble

Ce didacticiel vous montre comment récupérer une base de données SQL Azure à l’aide de la géo-restauration dans le [portail Azure](http://manage.windowsazure.com). La géo-restauration est la protection de récupération d’urgence de base qui est fournie pour tous les niveaux de service De base, Standard et Premium des bases de données SQL Azure.

## Restrictions et sécurité

* La géo-restauration est activée pour tous les niveaux de service De base, Standard et Premium.

* Les périodes de rétention des sauvegardes sont les suivantes : 7 jours pour le niveau De base, 14 jours pour le niveau Standard et 35 jours pour le niveau Premium.

* Seul l’administrateur ou co-administrateur de l’abonnement est habilité à envoyer une demande de restauration.

* Le compte de connexion du principal au niveau du serveur sera le propriétaire de la base de données restaurée.

* La géo-restauration n’est pas disponible pour les niveaux de service des éditions Web et Business.
 
	* Si vous disposez d’une base de données Web ou Business, vous pouvez utiliser la copie de base de données pour créer une copie de votre base de données respectant la cohérence transactionnelle des données, puis exporter la base de données copiée vers un compte de stockage Microsoft Azure. Pour plus d’informations, consultez [Procédure : utilisation de la copie de base de données (base de données SQL Azure)](http://msdn.microsoft.com/library/azure/ff951631.aspx) et [Procédure : utilisation du service d’importation et d’exportation dans Azure SQL Database](http://msdn.microsoft.com/library/azure/hh335292.aspx).

	* Les éditions Web et Business seront mises hors service en septembre 2015. Pour en savoir plus, consultez [Forum aux questions sur la disparition des éditions Web et Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).

## Procédure : récupération d’une base de données SQL Azure à l’aide de la géo-restauration dans le portail Azure

> [AZURE.VIDEO restore-a-sql-database-using-geo-restore]

1. Connectez-vous au [portail Azure](http://manage.windowsazure.com) en utilisant votre compte Microsoft, puis sélectionnez **Bases de données SQL**.

2. Dans le volet de navigation gauche, cliquez sur **BASES DE DONNÉES SQL**.

3. En haut de la page, cliquez sur **SERVEURS**.

4. Dans la liste **SERVEURS**, cliquez sur le serveur **Détérioré**.

4. En haut de la page, cliquez sur **SAUVEGARDES**.

5. Cliquez sur la base de données à restaurer.

6. En bas de la page dans la barre de commandes, cliquez sur **Restaurer**. Cette action ouvre la boîte de dialogue **Spécifier les paramètres de restauration**.

7. Choisissez le **NOM DE LA BASE DE DONNÉES** et le **SERVEUR CIBLE** à utiliser pour restaurer votre base de données. Un nom de base de données vous est proposé par défaut, mais vous pouvez le changer.

9. Cochez la case pour envoyer la demande de restauration.

Le processus de restauration peut prendre du temps. Pour surveiller l’état de la restauration, cliquez sur l’icône d’état en bas à droite de la page dans la barre de commandes, puis cliquez sur **DÉTAILS**.

## Étapes suivantes

Pour plus d’informations, consultez les liens suivants :

[Restaurer une base de données SQL Azure à l’aide d’une restauration jusqu’à une date et heure dans le portail Azure](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Restaurer une base de données SQL Azure supprimée dans le portail Azure](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Continuité de l’activité des bases de données SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sauvegarde et restauration de base de données SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Géo-restauration de base de données SQL Azure (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

<!---HONumber=Oct15_HO3-->