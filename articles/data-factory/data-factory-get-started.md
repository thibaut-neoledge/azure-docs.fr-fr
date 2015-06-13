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

Le didacticiel dans cet article vous aide à commencer rapidement à l'aide du service de fabrique de données Azure. Dans ce didacticiel, vous créez une fabrique de données Azure et créer un pipeline dans la fabrique de données pour copier des données à partir d'un stockage d'objets blob Azure dans une base de données SQL Azure.

> [AZURE.NOTE]Pour une présentation détaillée du service Factory de données, consultez la [Introduction à la fabrique de données Azure][data-factory-introduction] l'article.

##Configuration requise pour le didacticiel
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement Azure**. Si vous n'êtes pas abonné, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Consultez le [version d'évaluation gratuite][azure-free-trial] article pour plus d'informations.
- **Compte de stockage azure**. Vous allez utiliser le stockage d'objets blob en tant qu'un **source** magasin de données de ce didacticiel. Si vous n'avez pas un compte de stockage Azure, consultez le [créer un compte de stockage][data-factory-create-storage] article pour en créer un.
- **Base de données SQL Azure**. Vous allez utiliser une base de données SQL Azure comme un **destination** magasin de données de ce didacticiel. Si vous n'avez pas une base de données SQL Azure que vous pouvez utiliser dans le didacticiel, voir [comment créer et configurer une base de données SQL Azure][data-factory-create-sql-database] pour en créer un.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Vous allez utiliser SQL Server Management Studio ou Visual Studio pour créer une base de données exemple et afficher les données de résultat de la base de données.  

### Collecter le nom de compte et clé de compte pour votre compte de stockage Azure
Vous devez le nom de compte et la clé de compte de votre compte de stockage Windows Azure pour effectuer ce didacticiel. Notez le **nom de compte** et **clé de compte** pour votre compte de stockage Windows Azure en suivant les instructions ci-dessous :

1. Connexion à la [portail Azure Preview][azure-preview-portal].
2. Cliquez sur **Parcourir** hub sur la gauche et sélectionnez **comptes de stockage**.
3. Dans le **comptes de stockage** lame, sélectionnez le **compte de stockage Azure** que vous souhaitez utiliser dans ce didacticiel.
4. Dans le **stockage** lame, cliquez sur **clés** vignette.
5. Dans le **Gérer les clés** lame, cliquez sur **copie** bouton (image) à côté de **nom de compte de stockage** texte boîte et enregistrer/coller il quelque part (par exemple : dans un fichier texte).  
6. Répétez l'étape précédente pour copier ou noter le **clé d'accès primaire**.
7. Fermez toutes les lames en cliquant sur **X**.

### Collecter le nom du serveur, nom de la base de données et compte d'utilisateur pour votre base de données SQL Azure
Vous devez les noms du serveur SQL Azure, base de données et les utilisateurs pour effectuer ce didacticiel. Notez les noms des **server**, **base de données**, et **utilisateur** pour votre base de données SQL Azure en suivant les instructions ci-dessous :

1. Dans le **portail Azure Preview**, cliquez sur **Parcourir** sur la gauche et sélectionnez **bases de données SQL**.
2. Dans le **lame de bases de données SQL**, sélectionnez le **base de données** que vous souhaitez utiliser dans ce didacticiel. Notez le **nom de base de données**.  
3. Dans le **base de données SQL** lame, cliquez sur **propriétés** vignette.
4. Notez que les valeurs de **nom du serveur** et **connexion d'ADMIN SERVER**.
5. Fermez toutes les lames en cliquant sur **X**.

### Autoriser les services Azure à accéder au serveur SQL Azure
Vérifiez que **Autoriser l'accès aux services Azure** définition éteint **ON** pour votre serveur SQL Azure afin que le service de fabrique de données peut accéder à votre serveur SQL Azure. Pour vérifier et activer ce paramètre, procédez comme suit :

1. Cliquez sur **Parcourir** concentrateur à gauche et cliquez sur **serveurs SQL**.
2. Sélectionnez **votre serveur**, puis cliquez sur **paramètres** sur la **SQL SERVER** lame.
3. Dans le **paramètres** lame, cliquez sur **pare-feu**.
4. Dans le **des paramètres de pare-feu** lame, cliquez sur **ON** pour **Autoriser l'accès aux services Azure**.
5. Fermez toutes les lames en cliquant sur **X**.

### Préparer le stockage d'objets blob Azure et la base de données SQL Azure pour le didacticiel
À présent, préparez votre base de données SQL Azure et le stockage d'objets blob Azure pour le didacticiel en effectuant les étapes suivantes :

1. Lancez le bloc-notes, collez le texte suivant et enregistrez-le en tant que **emp.txt** à **C:\ADFGetStarted** dossier sur votre disque dur.

        John, Doe
		Jane, Doe

2. Utiliser des outils tels que [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) pour créer le **adftutorial** conteneur et pour télécharger le **emp.txt** fichier au conteneur.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Utiliser le script SQL suivant pour créer le **emp** table dans votre base de données SQL Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Si vous disposez de SQL Server 2012/2014 installé sur votre ordinateur :** suivez les instructions de [étape 2: se connecter à la base de données SQL de la gestion de base de données SQL à l'aide de SQL Server Management Studio][sql-management-studio] article pour vous connecter à votre serveur SQL Azure et exécutez le script SQL. Notez que cet article utilise le portail de gestion de version (http://manage.windowsazure.com), pas la version préliminaire du portail (http://portal.azure.com), pour configurer le pare-feu pour le serveur SQL Azure.

	**Si vous avez Visual Studio 2013 est installé sur votre ordinateur :** dans les [portail Azure Preview](http://portal.azure.com), cliquez sur **Parcourir** hub sur la gauche, cliquez sur **serveurs SQL**, sélectionnez votre base de données, puis cliquez sur **ouvert dans Visual Studio** dans la barre d'outils pour vous connecter à votre serveur SQL Azure et exécutez le script. Si votre client n'est pas autorisé à accéder au serveur SQL Azure, vous devez configurer le pare-feu pour votre serveur SQL Azure autoriser l'accès à partir de votre ordinateur (adresse IP). Consultez l'article ci-dessus pour savoir comment configurer le pare-feu pour votre serveur SQL Azure.


Effectuez les actions suivantes :

- Cliquez sur [à l'aide de l'éditeur de données fabrique](data-factory-get-started-using-editor.md) lien en haut pour effectuer le didacticiel à l'aide de données fabrique éditeur, qui fait partie du portail Azure.
- Cliquez sur [à l'aide de PowerShell](data-factory-monitor-manage-using-powershell.md) lien en haut pour effectuer le didacticiel à l'aide de PowerShell Azure.


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

<!---HONumber=GIT-SubDir--> 