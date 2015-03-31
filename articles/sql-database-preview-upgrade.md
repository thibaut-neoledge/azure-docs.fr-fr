<properties 
	pageTitle="Installation de la dernière mise à jour V12 de la base de données SQL (version préliminaire)" 
	description="Installation de la dernière mise à jour V12 de la base de données SQL (version préliminaire)" 
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
	ms.date="12/11/2014" 
	ms.author="sonalm"/>



# Installation de la dernière mise à jour V12 de la base de données SQL (version préliminaire)


[Inscrivez-vous](https://portal.azure.com) pour la dernière mise à jour de la base de données SQL V12 pour tirer parti de la nouvelle génération de  bases de données SQL sur Microsoft Azure. Tout d'abord, vous avez besoin d'un abonnement à Microsoft Azure. Inscrivez-vous à une [version d'évaluation gratuite Azure](http://azure.microsoft.com/pricing/free-trial) et examiner la [tarification](http://azure.microsoft.com/pricing/details/sql-database) pour plus d'informations. 

## Mise à niveau d'un serveur sur place avec la dernière mise à jour de la base de données SQL ##

| Mise à niveau  | Capture d'écran |
| :--- | :--- |
| 1. Connectez-vous sur le site [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. Cliquez sur **PARCOURIR**. | ![Browse Services][2] |
| 3.	Cliquez sur **Serveurs SQL**. Une liste de noms des serveurs SQL s'affiche. | ![Select SQL Server service][3] |
| 4. Sélectionnez le serveur à copier sur un nouveau serveur sur lequel la mise à jour de la base de données SQL est activée. | ![Shows a list of SQL Servers][4] |
| 5. Cliquez sur **Dernière mise à jour V12 de la base de données SQL**. | ![Latest preview feature][5] |
| 6. Cliquez sur **METTRE À JOUR CE SERVEUR**. | ![Upgrades the SQL Server to the preview][6] |

> [AZURE.NOTE] **IMPORTANT** : quand vous sélectionnez l'option de mise à jour, votre serveur et les bases de données qu'il contient sont activés avec les fonctionnalités de la mise à jour V12 de la base de données SQL, et vous ne pouvez pas annuler cette opération. Pour mettre à jour des serveurs vers la mise à jour V12 de la base de données SQL, vous avez besoin d'un niveau de service De base, Standard ou Premium. Pour plus d'informations sur les niveaux de service, consultez [Mise à jour des bases de données SQL des éditions Web et Business vers les nouveaux niveaux de Service](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/).

> **IMPORTANT** : la géo-réplication n'est pas prise en charge avec la mise à jour V12 de la base de données SQL (version préliminaire). Pour plus d'informations, consultez [Planification et préparation de la mise à jour vers la version préliminaire V12 de la base de données SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade).


Quand vous cliquez sur l'option **METTRE À JOUR CE SERVEUR**, le volet qui s'ouvre affiche un message sur le processus de validation. 

- Le processus de validation vérifie le niveau de service de votre base de données et si la géo-réplication est activée. Le volet affiche les résultats une fois la validation terminée. 
- À la fin du processus de validation, une liste de noms de base de données ne disposant pas de la configuration requise pour l'installation de la mise à jour V12 de la base de données SQL s'affiche. **Vous devez modifier chacune de ces bases de données pour pouvoir installer la mise à jour V12 de la base de données SQL**.
- Quand vous cliquez sur un nom de base de données, un nouveau volet vous recommande un niveau de tarification de service en fonction de votre utilisation actuelle. Vous pouvez également parcourir les différents niveaux de tarification et sélectionner celui qui convient le mieux à votre environnement. Toutes les bases de données configurées pour la géo-réplication doivent être reconfigurées de sorte à arrêter la réplication. 
- Notez que la recommandation sur le niveau de tarification ne s'affiche pas en cas de données insuffisantes. 

| Action | Capture d'écran |
| :--- | :--- |
| 7. Une fois que vous avez apporté les modifications permettant de préparer votre serveur pour la mise à jour, tapez le nom du serveur à mettre à jour et cliquez sur **OK**. | ![Confirm the server name to upgrade][7] |
| 8. Le processus de mise à jour est lancé. La mise à jour peut prendre jusqu'à 24 heures. Pendant cette période, toutes les bases de données sur ce serveur restent en ligne, mais les actions de gestion du serveur et des bases de données sont restreintes. Une fois le processus terminé, l'état **Activé** s'affiche dans le volet du serveur. | ![Confirms preview features are enabled][8] |
 

# Liens connexes  #

-  [Nouveautés de la dernière mise à jour V12 de la base de données SQL (version préliminaire)](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Planification et préparation de l'installation de la dernière mise à jour V12 de la base de données SQL (version préliminaire)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png
<!--HONumber=47-->
