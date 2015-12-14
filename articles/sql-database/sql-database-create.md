<properties 
	pageTitle="Création d'une base de données dans la mise à jour V12 de la base de données SQL" 
	description="Indique comment créer une base de données dans la mise à jour V12 de la base de données SQL Azure" 
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
	ms.date="04/28/2015" 
	ms.author="sonalm"/>


# Création d'une base de données dans SQL Database V12


<!--
True author is: authors="sonalmm" , ms.author="sonalm".
-->


[Inscrivez-vous](https://portal.azure.com) pour la base de données SQL V12 [(version préliminaire dans certaines régions)](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable), pour tirer parti de la nouvelle génération de bases de données SQL sur Microsoft Azure. Pour commencer, vous avez besoin d'un abonnement à Microsoft Azure. Inscrivez-vous à une [version d'évaluation gratuite Azure](http://azure.microsoft.com/pricing/free-trial) et examinez la [tarification](http://azure.microsoft.com/pricing/details/sql-database) pour plus d'informations.


| Création d’une base de données | Capture d’écran |
| :--- | :--- |
| 1\. Connectez-vous au site [http://portal.azure.com/](http://portal.azure.com/). | ![Nouveau portail Azure Classic][1] |
| 2\. En bas à gauche de la page, cliquez sur **Nouveau**. | ![Initier un nouveau service][2]|
| 3\. Cliquez sur **Base de données SQL**.| ![Différents services à sélectionner][3] |
| 4\. Un volet **Base de données SQL** s'ouvre. Dans le champ **Nom**, spécifiez un nom de base de données. | ![Nommer la base de données][4] |
| 5\. Dans le **volet Base de données SQL**, cliquez sur **SERVEUR**. Dans le volet **Serveur** qui s'ouvre, vous pouvez choisir entre la création d'un serveur ou l'utilisation d'un serveur existant.| ![sélectionner le type de serveur][4] |
|5a. Si vous sélectionnez l'option **Utiliser un serveur existant**, sélectionnez le serveur de votre choix, puis cliquez sur **Sélectionner**. Ensuite, effectuez toutes les actions à partir de l’étape 6.| ![sélectionner un serveur dans la liste][5]| 
|5b. Si vous sélectionnez **Créer un nouveau serveur**, le volet **Nouveau serveur** s'ouvre. Spécifiez le nom du serveur, le nom de connexion d’administrateur de serveur et le mot de passe. Cliquez sur **Emplacement** pour sélectionner l'emplacement du serveur. | ![Création complète de nouvelles options de serveur][9]| 
|5c.Le volet **Nouveau serveur** vous donne la possibilité de créer le nouveau serveur avec les mises à jour V12. Pour en savoir plus sur les fonctionnalités des serveurs V12, consultez [Nouveautés de la base de données SQL V12](sql-database-v12-whats-new.md).| ![Sélectionner le serveur V12][6]|
|5d. Effectuez vos sélections dans le volet **Nouveau serveur** et cliquez sur **OK**. Ce lien vous amènera au volet **Base de données SQL** pour effectuer le reste des actions de création de base de données. | ![Nouvelles actions complètes du volet Serveur][8]|
|6\. Cliquez sur **Sélectionner la source**. Les différents types de sources vous permettant de créer une base de données sont : une base de données vide, une base de données d’exemple ou à partir d’une sauvegarde de base de données.| ![Sélectionner la source de la base de données][10]|
|7\. Puis, dans le volet **Base de données SQL**, cliquez sur **NIVEAU DE TARIFICATION**. Vous pouvez sélectionner le niveau de tarification recommandé ou **afficher tous** les niveaux de tarification disponibles. Effectuez votre choix, puis cliquez sur **Sélectionner**. <p> Pour plus d'informations sur les niveaux de tarification, consultez [Mise à jour des bases de données SQL des éditions Web et Business vers les nouveaux niveaux de service](./sql-database-upgrade-new-service-tiers/) et [Niveaux de service et niveaux de performances de la base de données SQL Azure](sql-database-service-tiers.md). |![Sélectionner un niveau de tarification][7]
| 8\. Puis, dans le volet **Base de données SQL**, cliquez sur **Configuration facultative**, effectuez vos choix, puis cliquez sur **OK**. 
| 9\. Lorsque vous sélectionnez un serveur existant, le **groupe de ressources** et l'**abonnement** sont déjà sélectionnés pour vous. Dans le volet **Base de données SQL** lame, une icône de verrou s'affiche à côté des valeurs **Groupe de ressources** et **Abonnement**. Si vous créez un nouveau serveur, vous devez sélectionner ou créer un groupe de ressources. Pour plus d'informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](resource-group-overview.md).|![Spécifier un groupe de ressources][11]
| 10\. Cliquez sur **Create**. Une base de données avec les fonctionnalités de la base de données SQL V12 est créée. |![Crée une base de données][12]

## Liens connexes

- [Nouveautés de la base de données SQL V12](sql-database-v12-whats-new.md)
- [Planification et préparation de l'installation de la mise à niveau vers la base de données SQL Azure V12](sql-database-v12-plan-prepare-upgrade.md)

<!--Image references-->
[1]: ./media/sql-database-create/firstscreenportal.png
[2]: ./media/sql-database-create/new.png
[3]: ./media/sql-database-create/sqldatabase.png
[4]: ./media/sql-database-create/databasename.png
[5]: ./media/sql-database-create/useexistingserver.PNG
[6]: ./media/sql-database-create/v12server.PNG
[7]: ./media/sql-database-create/pricingtierdetails.png
[8]: ./media/sql-database-create/finishnewserverblade.png
[9]: ./media/sql-database-create/createnewserver.png
[10]: ./media/sql-database-create/selectsource.png
[11]: ./media/sql-database-create/resourcegroup.png
[12]: ./media/sql-database-create/create.png

 

<!---HONumber=AcomDC_1203_2015-->