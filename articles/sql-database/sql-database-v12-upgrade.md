<properties 
	pageTitle="Mise à niveau vers la version 12 de la base de données SQL" 
	description="Explique comment mettre à niveau une base de données SQL Microsoft Azure vers la version 12." 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="05/15/2015" 
	ms.author="sonalm"/>


# Mise à niveau vers la base de données SQL version 12 installée


[Inscrivez-vous](https://portal.azure.com) à la base de données SQL version 12, afin de tirer parti de la nouvelle génération de bases de données SQL sur Microsoft Azure. Tout d’abord, vous avez besoin d’un abonnement à Microsoft Azure. Inscrivez-vous à la version d’[essai gratuit de Microsoft Azure](http://azure.microsoft.com/pricing/free-trial) et examinez les informations sur la [tarification](http://azure.microsoft.com/pricing/details/sql-database).


## Étapes de la mise à niveau vers la base de données SQL version 12


| Étape | Capture d’écran |
| :--- | :--- |
| 1. Connectez-vous au site [http://portal.azure.com/](http://portal.azure.com/). | ![Nouveau portail Microsoft Azure][1] |
| 2. Cliquez sur **PARCOURIR**. | ![Parcourir les services][2] |
| 3. Cliquez sur **Serveurs SQL Server**. Une liste des noms des serveurs SQL s’affiche. | ![Sélectionner le service SQL Server][3] |
| 4. Sélectionnez le serveur à copier sur un nouveau serveur sur lequel la mise à jour de la base de données SQL est activée. | ![Affiche la liste des serveurs SQL Server][4] |
| 5. Cliquez sur **Dernière mise à jour de la base de données SQL version 12**. | ![Dernière fonctionnalité préliminaire][5] |
| 6. Cliquez sur **METTRE À NIVEAU LE SERVEUR**. | ![Met à niveau SQL Server vers la version préliminaire][6] |


> [AZURE.NOTE]Lorsque vous sélectionnez l’option de mise à niveau, votre serveur et les bases de données qu’il contient sont activés avec les fonctionnalités de la version 12 de la base de données SQL. Vous ne pouvez pas annuler cette opération. Pour mettre à niveau des serveurs vers la version 12 de la base de données SQL, vous avez besoin d’un niveau de service de base, Standard ou Premium. Pour en savoir plus sur les niveaux de service, voir [Mise à jour des bases de données SQL des éditions Web ou Business vers les nouveaux niveaux de service](sql-database-upgrade-new-service-tiers.md).


> [AZURE.IMPORTANT]La géo-réplication n’est pas prise en charge avec la version 12 de la base de données SQL (version préliminaire). Pour en savoir plus, voir [Planification et préparation de la mise à niveau vers la version préliminaire V12 de la base de données SQL Azure](sql-database-v12-plan-prepare-upgrade.md).


Lorsque vous cliquez sur l’option **METTRE À NIVEAU LE SERVEUR**, le panneau qui s’ouvre affiche un message sur le processus de validation.


- Le processus de validation vérifie le niveau de service de votre base de données et si la géo-réplication est activée. Le volet affiche les résultats une fois la validation terminée. 
- À la fin du processus de validation, le système affiche une liste de noms de base de données que vous devez modifier pour répondre aux exigences de la mise à niveau vers la version 12 de la base de données SQL.
 - **Vous devez effectuer ces actions pour que chaque base de données puisse être mise à niveau vers la version 12 de la base de données SQL**.
- Quand vous cliquez sur un nom de base de données, un nouveau volet vous recommande un niveau de tarification de service en fonction de votre utilisation actuelle. Vous pouvez également parcourir les différents niveaux de tarification et sélectionner celui qui convient le mieux à votre environnement. Toutes les bases de données configurées pour la géo-réplication doivent être reconfigurées de sorte à arrêter la réplication. 
- Notez que la recommandation sur le niveau de tarification ne s’affiche pas en cas de données insuffisantes. 


| Action | Capture d’écran |
| :--- | :--- |
| 7. Une fois que vous avez apporté les modifications permettant de préparer votre serveur à la mise à niveau, saisissez le nom du serveur à mettre à niveau et cliquez sur **OK**. | ![Confirmer le nom du serveur à mettre à niveau][7] |
| 8. Le processus de mise à jour est lancé. La mise à niveau peut prendre jusqu’à 24 heures. Pendant cette période, toutes les bases de données sur ce serveur restent en ligne, mais les actions de gestion du serveur et des bases de données sont restreintes. Une fois le processus terminé, l’état **Activé** s’affiche dans le panneau du serveur. | ![Confirme les fonctions de la version préliminaire qui seront activées][8] |


## Applets de commande PowerShell


Des applets de commande PowerShell sont disponibles pour démarrer, arrêter ou surveiller une mise à niveau vers la version 12 de la base de données SQL Microsoft Azure, à partir d’une version 11 ou antérieure.


Pour en savoir plus sur ces applets, voir :


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


L’applet de commande Stop- signifie « annuler », et non « interrompre ». Il est impossible de reprendre une mise à niveau autrement qu’en la recommençant depuis le début. L’applet de commande Stop- nettoie et libère toutes les ressources appropriées.


## Liens connexes

-  [Nouveautés de la base de données SQL V12](sql-database-v12-whats-new.md)
- [Planification et préparation de l’installation de la mise à niveau V12 de la base de données SQL](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/firstscreenportal.png
[2]: ./media/sql-database-v12-upgrade/browse.png
[3]: ./media/sql-database-v12-upgrade/sqlserver.png
[4]: ./media/sql-database-v12-upgrade/sqlserverlist.png
[5]: ./media/sql-database-v12-upgrade/latestprview.png
[6]: ./media/sql-database-v12-upgrade/upgrade.png
[7]: ./media/sql-database-v12-upgrade/typeservername.png
[8]: ./media/sql-database-v12-upgrade/enabled.png

<!---HONumber=58--> 