<properties 
	pageTitle="Création d'une base de données dans la dernière mise à jour V12 de la base de données SQL (version préliminaire)" 
	description="Création d'une base de données dans la dernière mise à jour V12 de la base de données SQL (version préliminaire)" 
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


# Création d'une base de données dans la dernière mise à jour V12 de la base de données SQL (version préliminaire)

[Inscrivez-vous](https://portal.azure.com) pour la dernière mise à jour de base de données V12 de SQL pour tirer parti de la nouvelle génération de bases de données SQL sur Microsoft Azure. Pour commencer, vous avez besoin d'un abonnement à Microsoft Azure. Inscrivez-vous à une [version d'évaluation gratuite Azure](http://azure.microsoft.com/pricing/free-trial) et examinez la [tarification](http://azure.microsoft.com/pricing/details/sql-database) pour plus d'informations. 


| Création d'une base de données | Capture d'écran |
| :--- | :--- |
| 1. Connectez-vous sur le site [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. En bas à gauche de la page, cliquez sur **Nouveau**. | ![Initiate New service][2]|
| 3. Cliquez sur **Base de données SQL**.| ![Different services to select from][3] |
| 4. Un volet **Base de données SQL** s'ouvre. Dans le champ **Nom**, spécifiez un nom de base de données. | ![Name the database][4] |
| 5. Dans le **volet Base de données SQL**, cliquez sur **SERVEUR**. Dans le volet **Serveur** qui s'ouvre, vous pouvez choisir entre la création d'un serveur ou l'utilisation d'un serveur existant.| ![select type of server][4] |
|5a. Si vous sélectionnez l'option **Utiliser un serveur existant**, sélectionnez le serveur de votre choix, puis cliquez sur **Sélectionner**. Ensuite, effectuez toutes les actions à partir de l'étape 6.| ![select a server from the list][5]| 
|5b.   Si vous sélectionnez **Créer un nouveau serveur**, le volet **Nouveau serveur** s'ouvre. Spécifiez le nom du serveur, le nom de connexion d'administrateur de serveur et le mot de passe. Cliquez sur **Emplacement**   pour sélectionner l'emplacement du serveur. | ![Complete create new server options][9]| 
|5C.Le volet **Nouveau serveur** vous donne la possibilité de créer le nouveau serveur avec les mises à jour V12. Pour en savoir plus sur les fonctionnalités des serveurs V12, consultez [Nouveautés de la base de données SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/).| ![Select V12 server][6]|
|5d. Effectuez vos sélections dans le volet **Nouveau serveur** et cliquez sur **OK**. Ce lien vous amènera au volet **Base de données SQL** pour effectuer le reste des actions de création de base de données. | ![Complete New Server blade actions][8]|
|6. Cliquez sur **Sélectionner la source**. Les différents types de sources vous permettant de créer une base de données sont : une base de données vide, une base de données d'exemple ou à partir d'une sauvegarde de base de données.| ![Select the source for the database][10]|
|7. Puis, dans le volet **Base de données SQL**, cliquez sur **NIVEAU DE TARIFICATION**. Vous pouvez sélectionner le niveau de tarification recommandé ou parcourir tous les niveaux de tarification disponibles. Effectuez votre choix, puis cliquez sur **Sélectionner**. <p> Pour plus d'informations sur les niveaux de tarification, consultez [Mise à jour des bases de données SQL des éditions Web et Business vers les nouveaux niveaux de service](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) et [Niveaux de service et niveaux de performances de la base de données SQL Azure](http://msdn.microsoft.com/library/azure/dn741336.aspx). |![Select a pricing tier][7]
| 8. Puis, dans le volet **Base de données SQL**, cliquez sur **Configuration facultative**, effectuez vos choix, puis cliquez sur **OK**. 
| 9. Dans le volet **Base de données SQL**, cliquez sur **Groupes de ressources**. Créez un groupe de ressources ou sélectionnez un groupe de ressources existant dans la liste. Cliquez sur **OK**. Pour plus d'informations, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure.](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups)|![Specify Resource group][11]
| 10. Dans le volet **Base de données SQL**, cliquez sur **ABONNEMENT**.Un volet **Abonnement** s'ouvre. Sélectionnez un abonnement Azure qui prend en charge votre choix de serveur. Notez qu'en fonction de l'abonnement, les paramètres du serveur peuvent changer.| ![Select subscription.][13]
| 11. Cliquez sur **Créer**. Une base de données avec les fonctionnalités de la mise à jour V12 de la base de données SQL est créée. |![Creates a new database][12]

# Liens connexes  #

-  [Nouveautés de la base de données SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Planification et préparation de l'installation de la mise à niveau vers la base de données SQL Azure V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)

<!--Image references-->
[1]: ./media/sql-database-preview-create/firstscreenportal.png
[2]: ./media/sql-database-preview-create/new.png
[3]: ./media/sql-database-preview-create/sqldatabase.png
[4]: ./media/sql-database-preview-create/databasename.png
[5]: ./media/sql-database-preview-create/useexistingserver.PNG
[6]: ./media/sql-database-preview-create/v12server.PNG
[7]: ./media/sql-database-preview-create/pricingtierdetails.png
[8]: ./media/sql-database-preview-create/finishnewserverblade.png
[9]: ./media/sql-database-preview-create/createnewserver.png
[10]: ./media/sql-database-preview-create/selectsource.png
[11]: ./media/sql-database-preview-create/resourcegroup.png
[12]: ./media/sql-database-preview-create/create.png
[13]: ./media/sql-database-preview-create/subscription.PNG


<!--HONumber=47-->
