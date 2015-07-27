<properties
	pageTitle="Prise en main d'Azure Data Factory"
	description="Ce didacticiel vous montre comment créer un exemple de pipeline de données qui copie les données d'un objet blob vers une instance de base de données SQL Azure."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="05/04/2015"
	ms.author="spelluru"/>

# Prise en main d'Azure Data Factory
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

Le didacticiel de cet article vous aide à comprendre rapidement comment utiliser le service Azure Data Factory. Ce didacticiel crée une fabrique de données Azure et un pipeline dans la fabrique de données pour copier des données d'un stockage d'objets blob Azure dans une base de données SQL Azure.

> [AZURE.NOTE]Pour obtenir une présentation détaillée du service Data Factory, consultez l'article [Présentation d'Azure Data Factory][data-factory-introduction].

##Configuration requise pour le didacticiel
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement Azure**. Si vous n'êtes pas abonné, vous pouvez créer un compte d'essai gratuit en quelques minutes. Consultez l'article [Essai gratuit][azure-free-trial] pour plus d'informations.
- **Compte Azure Storage**. Dans le cadre de ce didacticiel, le stockage d'objets blob est utilisé comme magasin de données **source**. Si vous n'avez pas de compte de stockage Azure, consultez l'article [Créer un compte de stockage][data-factory-create-storage] pour découvrir comment en créer un.
- **Base de données SQL Azure**. Vous allez utiliser une base de données SQL Azure comme magasin de données **cible** dans ce didacticiel. Si vous n'avez pas de base de données SQL Azure pouvant être utilisée pour le didacticiel, consultez [Comment créer et configurer une base de données SQL Azure][data-factory-create-sql-database] pour en créer une.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Vous allez utiliser SQL Server Management Studio ou Visual Studio pour créer un exemple de base de données et afficher les données de résultat dans la base de données.  

### Collecter le nom et la clé de votre compte de stockage Azure
Pour réaliser ce didacticiel, vous avez besoin du nom et de la clé de votre compte de stockage Azure. Notez le **nom** et la **clé** de votre compte de stockage Azure en suivant les instructions ci-dessous :

1. Connectez-vous au [portail Azure en version préliminaire][azure-preview-portal].
2. Cliquez sur le hub **PARCOURIR** situé à gauche et sélectionnez **Comptes de stockage**.
3. Dans le panneau **Comptes de stockage**, sélectionnez le **compte de stockage Azure** que vous souhaitez utiliser dans ce didacticiel.
4. Dans le panneau **STOCKAGE**, cliquez sur la vignette **CLÉS**.
5. Dans le panneau **Gérer les clés**, cliquez sur le bouton **copier** (image) situé en regard de la zone de texte **NOM DU COMPTE DE STOCKAGE** et enregistrez/collez-la quelque part (dans un fichier texte, par exemple).  
6. Répétez l'étape précédente pour copier ou noter la **CLÉ D'ACCÈS PRIMAIRE**.
7. Fermez tous les panneaux en cliquant sur **X**.

### Collecter le nom du serveur, le nom de la base de données et le compte d'utilisateur pour votre base de données SQL Azure
Pour réaliser ce didacticiel, vous avez besoin des noms du serveur SQL Azure, de la base de données et de l'utilisateur. Notez les noms du **serveur**, de la **base de données** et de l'**utilisateur** pour votre base de données SQL Azure en suivant les instructions ci-dessous :

1. Dans le **portail Azure en version préliminaire**, cliquez sur **PARCOURIR** sur la gauche et sélectionnez **Bases de données SQL**.
2. Dans le panneau **Bases de données SQL**, sélectionnez la **base de données** que vous souhaitez utiliser dans le cadre de ce didacticiel. Notez le **nom de la base de données**.  
3. Dans le panneau **BASE DE DONNÉES SQL**, cliquez sur la vignette **PROPRIÉTÉS**.
4. Notez les valeurs de **NOM DU SERVEUR** et de **CONNEXION D'ADMINISTRATEUR DU SERVEUR**.
5. Fermez tous les panneaux en cliquant sur **X**.

### Autoriser les services Azure à accéder au serveur SQL Azure
Vérifiez que le paramètre **Autoriser l'accès aux services Azure** est **ACTIVÉ** pour votre serveur SQL Azure pour que le service Data Factory puisse accéder à votre serveur SQL Azure. Pour vérifier et activer ce paramètre, procédez comme suit :

1. Cliquez sur le hub **PARCOURIR** situé à gauche, puis sur **Serveurs SQL**.
2. Sélectionnez **votre serveur**, puis cliquez sur **PARAMÈTRES** dans le panneau **SQL SERVER**.
3. Dans le panneau **PARAMÈTRES**, cliquez sur **Pare-feu**.
4. Dans le panneau **Paramètres de pare-feu**, cliquez sur **ACTIVER** pour **Autoriser l'accès aux services Azure**.
5. Fermez tous les panneaux en cliquant sur **X**.

### Préparer le stockage d'objets blob Azure et la base de données SQL Azure pour le didacticiel
À présent, préparez votre stockage d'objets blob Azure et votre base de données SQL Azure pour ce didacticiel, en procédant comme suit :

1. Ouvrez le Bloc-notes, collez le texte suivant, puis enregistrez-le sous le nom **emp.txt** dans le dossier **C:\\ADFGetStarted** sur votre disque dur.

        John, Doe
		Jane, Doe

2. Utilisez des outils tels que l'[Explorateur Azure Storage](https://azurestorageexplorer.codeplex.com/) pour créer le conteneur **adftutorial** et charger le fichier **emp.txt** vers ce dernier.

    ![Explorateur du stockage Azure][image-data-factory-get-started-storage-explorer]
3. Utilisez le script SQL suivant pour créer la table **emp** dans votre base de données SQL Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Si SQL Server 2012/2014 est installé sur votre ordinateur :** suivez les instructions de l'[Étape 2 : connexion à la base de données SQL de l'article Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio][sql-management-studio] pour vous connecter à votre serveur SQL Azure et exécuter le script SQL. Notez que cet article utilise la version commerciale du portail de gestion (http://manage.windowsazure.com)), et non la version préliminaire du portail (http://portal.azure.com)), pour configurer le pare-feu d'un serveur SQL Azure.

	**Si Visual Studio 2013 est installé sur votre ordinateur :** dans le [portail Azure en version préliminaire](http://portal.azure.com), cliquez sur le hub **PARCOURIR** situé à gauche, puis sur **Serveurs SQL**, sélectionnez ensuite votre base de données, puis cliquez sur le bouton **Ouvrir dans Visual Studio** dans la barre d'outils pour vous connecter à votre serveur SQL Azure et exécuter le script. Si votre client n’est pas autorisé à accéder au serveur SQL Azure, vous devez configurer le pare-feu pour votre serveur SQL Azure afin d’autoriser l’accès à partir de votre ordinateur (adresse IP). Consultez l'article ci-dessus pour savoir comment configurer le pare-feu pour votre serveur SQL Azure.


Effectuez les actions suivantes :

- Cliquez sur le lien [Utilisation de Data Factory Editor](data-factory-get-started-using-editor.md) situé dans la partie supérieure pour effectuer le didacticiel à l'aide de Data Factory Editor, qui fait partie du portail Azure.
- Cliquez sur le lien [Utilisation de PowerShell](data-factory-monitor-manage-using-powershell.md) situé dans la partie supérieure pour effectuer le didacticiel à l'aide d'Azure PowerShell.


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png
 

<!---HONumber=July15_HO3-->