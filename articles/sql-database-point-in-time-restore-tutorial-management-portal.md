<properties 
   pageTitle="Restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure dans le portail Azure" 
   description="restauration jusqu'à une date et heure, base de données SQL Microsoft Azure, restaurer une base de données, récupérer une base de données, portail de gestion Azure, portail Azure" 
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

# Restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure dans le portail Azure

> [AZURE.SELECTOR]
- [Restauration jusqu'à une date et heure - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-powershell/)
- [Restauration jusqu'à une date et heure - API REST](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-rest/) 

## Présentation

Ce didacticiel vous montre comment restaurer une base de données SQL Azure à l'aide de la restauration jusqu'à une date et heure dans le [portail Azure](http://manage.windowsazure.com/). Une base de données SQL Azure présente des sauvegardes intégrées pour prendre en charge la restauration en libre-service jusqu'à une date et heure pour les niveaux de service De base, Standard et Premium.

La restauration jusqu'à une date et heure entraîne la création d'une nouvelle base de données. Le service sélectionne automatiquement le niveau de service en fonction de la sauvegarde utilisée au point de restauration. Assurez-vous que le quota disponible sur le serveur logique est suffisant pour permettre la création d'une autre base de données. Si vous avez besoin d'un quota supérieur, contactez le [support technique d'Azure](http://azure.microsoft.com/support/options/).

## Restrictions et sécurité

* La restauration jusqu'à une date et heure est activée pour tous les niveaux de service De base, Standard et Premium.

* Les périodes de rétention des sauvegardes sont les suivantes : 7 jours pour le niveau De base, 14 jours pour le niveau Standard et 35 jours pour le niveau Premium.
 
* Seul l'administrateur ou co-administrateur de l'abonnement est habilité à envoyer une demande de restauration.

* Le compte de connexion du principal au niveau du serveur sera le propriétaire de la base de données restaurée.

* La restauration jusqu'à une date et heure n'est pas disponible pour les niveaux de service des éditions Web et Business.
 
	* Si vous disposez d'une base de données Web ou Business, vous pouvez utiliser la copie de base de données pour créer une copie de votre base de données respectant la cohérence transactionnelle des données, puis exporter la base de données copiée vers un compte de stockage Microsoft Azure. Pour plus d'informations, consultez [Procédure : utilisation de la copie de base de données (base de données SQL Azure)](http://msdn.microsoft.com/library/azure/ff951631.aspx) et [Procédure : utilisation du service d'importation et d'exportation dans la base de données SQL Azure](http://msdn.microsoft.com/library/azure/hh335292.aspx).

	* Les éditions Web et Business seront mises hors service en septembre 2015. Pour plus d'informations, consultez [Forum aux questions sur la disparition des éditions Web et Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).

## Procédure : restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure dans le portail Azure

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Point-in-Time-Restore/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

1. Connectez-vous au portail de gestion Azure en utilisant votre compte Azure.

2. Dans le volet de navigation gauche, cliquez sur **BASES DE DONNÉES SQL**.
  
3. Dans la liste **BASES DE DONNÉES**, cliquez sur la base de données à restaurer. 

4. En bas de la page dans la barre de commandes, cliquez sur **RESTAURER**. Cette action ouvre la boîte de dialogue **Spécifier les paramètres de restauration**.

5. Choisissez un **NOM DE BASE DE DONNÉES**. Un nom de base de données vous est proposé par défaut, mais vous pouvez le changer.

6. Choisissez la date et l'heure jusqu'où votre base de données doit être restaurée. La date et l'heure doivent se trouver dans la période de rétention de la base de données.
	
7. Cochez la case pour envoyer la demande de restauration.

Le processus de restauration peut prendre du temps. Pour surveiller l'état de la restauration, cliquez sur l'icône d'état en bas à droite de la page dans la barre de commandes, puis cliquez sur **DÉTAILS**.

## Étapes suivantes

Pour plus d'informations, consultez les liens suivants :

[Continuité des activités de l'entreprise dans la base de données SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sauvegarde et restauration de base de données SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Restauration jusqu'à une date et heure d'une base de données SQL Azure (blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)
<!--HONumber=47-->
