<properties
	pageTitle="Didacticiel : Utilisation de l'activité de copie dans un pipeline Azure Data Factory"
	description="Ce didacticiel vous montre comment utiliser l'activité de copie dans un pipeline Azure Data Factory pour copier des données depuis un objet blob Azure vers une base de données SQL Azure."
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
	ms.topic="get-started-article" 
	ms.date="11/02/2015"
	ms.author="spelluru"/>

# Didacticiel : copie de données depuis un objet blob Azure vers Azure SQL
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)

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

1. Connectez-vous au [portail Azure][azure-portal].
2. Cliquez sur le hub **PARCOURIR** situé à gauche et sélectionnez **Comptes de stockage**.
3. Dans le panneau **Comptes de stockage**, sélectionnez le **compte de stockage Azure** que vous souhaitez utiliser dans ce didacticiel.
4. Dans le panneau **STOCKAGE**, cliquez sur la vignette **CLÉS**.
5. Dans le panneau **Gérer les clés**, cliquez sur le bouton **copier** (image) situé en regard de la zone de texte **NOM DU COMPTE DE STOCKAGE** et enregistrez/collez-la quelque part (dans un fichier texte, par exemple).  
6. Répétez l'étape précédente pour copier ou noter la **CLÉ D'ACCÈS PRIMAIRE**.
7. Fermez tous les panneaux en cliquant sur **X**.

### Collecter le nom du serveur, le nom de la base de données et le compte d'utilisateur pour votre base de données SQL Azure
Pour réaliser ce didacticiel, vous avez besoin des noms du serveur SQL Azure, de la base de données et de l'utilisateur. Notez les noms du **serveur**, de la **base de données** et de l'**utilisateur** pour votre base de données SQL Azure en suivant les instructions ci-dessous :

1. Dans le **portail Azure**, cliquez sur **PARCOURIR** dans le volet gauche et sélectionnez **Bases de données SQL**.
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

    ![Azure Storage Explorer](./media/data-factory-get-started/getstarted-storage-explorer.png)
3. Utilisez le script SQL suivant pour créer la table **emp** dans votre base de données SQL Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Si SQL Server 2012/2014 est installé sur votre ordinateur :** suivez les instructions de l'[Étape 2 : connexion à la base de données SQL de l'article Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio][sql-management-studio] pour vous connecter à votre serveur SQL Azure et exécuter le script SQL. Notez que cet article utilise le [portail Azure Classic](http://manage.windowsazure.com), et non le [portail Azure](http://portal.azure.com), pour configurer le pare-feu d’un serveur SQL Azure.

	**Si Visual Studio 2013 est installé sur votre ordinateur :** dans le [portail Azure](http://portal.azure.com), cliquez sur le hub **PARCOURIR** situé à gauche, puis sur **Serveurs SQL**, sélectionnez ensuite votre base de données, puis cliquez sur le bouton **Ouvrir dans Visual Studio** dans la barre d’outils pour vous connecter à votre serveur SQL Azure et exécuter le script. Si votre client n’est pas autorisé à accéder au serveur SQL Azure, vous devez configurer le pare-feu pour votre serveur SQL Azure afin d’autoriser l’accès à partir de votre ordinateur (adresse IP). Consultez l'article ci-dessus pour savoir comment configurer le pare-feu pour votre serveur SQL Azure.


Effectuez les actions suivantes :

- Cliquez sur le lien [Utilisation de Data Factory Editor](data-factory-get-started-using-editor.md) situé dans la partie supérieure pour effectuer le didacticiel à l’aide de Data Factory Editor, qui fait partie du portail Azure Classic.
- Cliquez sur le lien [Utilisation de PowerShell](data-factory-monitor-manage-using-powershell.md) situé dans la partie supérieure pour effectuer le didacticiel à l'aide d'Azure PowerShell.
- Cliquez sur le lien [Utilisation de Visual Studio](data-factory-get-started-using-vs.md) situé dans la partie supérieure pour suivre le didacticiel à l’aide de Visual Studio 2013.
 


<!--Link references-->
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database/sql-database-get-started.md

<!---HONumber=AcomDC_0107_2016-->