<properties linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="How to create &amp; provision" pageTitle="Getting started with SQL Database - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="" solutions="" writer="" manager="" editor="" />

Prise en main de la base de données SQL Azure
=============================================

Dans ce didacticiel, vous allez vous familiariser avec les concepts de base de l'administration de la base de données SQL Azure à l'aide du portail de gestion Azure. Si vous êtes néophyte en matière d'administration de base de données, vous pouvez suivre ces leçons pour acquérir les compétences essentielles en environ 30 minutes.

Ce didacticiel ne présuppose aucune expérience préalable de SQL Server ou de la base de données SQL Azure. À la fin de ce didacticiel, vous disposerez d'un exemple de base de données sur Azure et vous maîtriserez les tâches d'administration de base avec le portail de gestion.

Vous allez créer et mettre en service un exemple de base de données sur Azure, et interroger le système et les données utilisateur en utilisant Excel et d'autres applications.

Sommaire
--------

-   [Étape 1 : création d'un compte Azure](#Subscribe)
-   [Étape 2 : connexion à Azure et création d'une base de données](#Connect)
-   [Étape 3 : configuration du pare-feu](#ConfigFirewall)
-   [Étape 4 : ajout de données et d'un schéma avec un script Transact-SQL](#AddData)
-   [Étape 5 : création du schéma](#createschema)
-   [Étape 6 : insertion des données](#insertData)
-   [Étape 7 : interrogation des données de l'exemple et des données système dans le portail de gestion pour la base de données SQL](#QueryDBSysData)
-   [Étape 8 : création d'une connexion de base de données et attribution d'autorisations](#DBLogin)
-   [Étape 9 : connexion depuis d'autres applications](#ClientConnection)
-   [Étape 10 : configuration de la synchronisation des données SQL](#ConfigureDataSync)

Étape 1 : création d'un compte Azure
------------------------------------

1.  Ouvrez un navigateur Web et accédez à la page <http://www.windowsazure.com>. Pour commencer avec un compte gratuit, cliquez sur Free Trial dans le coin supérieur droit, puis suivez la procédure.

2.  Votre compte est maintenant créé. Vous êtes prêt à commencer.

Étape 2 : connexion à Azure et création d'une base de données
-------------------------------------------------------------

1.  Connectez-vous au [portail de gestion](http://manage.windowsazure.com). Un volet de navigation similaire à celui illustré ci-dessous apparaît.

    ![Image1](./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png)

2.  Cliquez sur **Nouveau** en bas de la page. Lorsque vous cliquez sur **Nouveau**, une liste des éléments que vous avez créés s'affiche à l'écran.

3.  Cliquez sur **Base de données SQL**, puis sur **Custom Create**.

    ![Image2](./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png)

    Cette option vous permet de créer en même temps un serveur dont vous serez l'administrateur. En tant qu'administrateur système, vous pouvez exécuter d'autres tâches, notamment vous connecter au portail de gestion pour la base de données SQL, comme vous le ferez plus tard dans ce didacticiel.

4.	Lorsque vous cliquez sur **Custom Create**, la page Paramètres de la base de données s'affiche. Sur cette page, vous fournissez les informations de base pour créer une base de données vide sur le serveur. L'ajout des tables et des données se fera à une étape ultérieure.

	Remplissez la page Paramètres de la base de données comme suit :

	![Image3](./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG)

	-   Entrez **School** comme nom de base de données.

	-   Utilisez les paramètres par défaut pour les options relatives à l'édition, la taille maximale et le classement.

	-   Choisissez **New SQL Database Server**. Lorsque vous sélectionnez un nouveau serveur, une seconde page s'affiche, dont nous allons nous servir pour définir le compte d'administrateur et la région.

	-   Cliquez ensuite sur la flèche pour passer à la page suivante.

5.	Remplissez la page Paramètres du serveur comme suit :

	![Image4](./media/sql-database-get-started/4ServerSettings_SQLTut.PNG)

    -   Entrez un nom d'administrateur (sans espace). La base de données SQL utilise l'authentification SQL sur une connexion chiffrée pour valider l'identité de l'utilisateur. Une nouvelle connexion d'authentification SQL Server dotée des autorisations d'administrateur est créée avec le nom indiqué. Le nom d'administrateur ne doit pas correspondre à un utilisateur Windows, ni à un Windows Live ID. L'authentification Windows n'est pas prise en charge sur la base de données SQL.

    -   Fournissez un mot de passe fort de plus de huit caractères, combinant des majuscules et des minuscules, et comportant un chiffre ou un symbole.

    -   Choisissez une région. La région détermine l'emplacement géographique du serveur. Choisissez une région qui est pertinente pour ce serveur, car il n'est pas possible de basculer facilement d'une région à l'autre. Choisissez un emplacement le plus proche possible de vous ou de vos utilisateurs. Vous économisez ainsi sur les coûts de la bande passante de sortie et réduisez la latence des données en maintenant l'application et la base de données Azure dans la même région.

    -   Veillez à ce que la case à cocher **Allow Azure Services to access this server** reste activée pour pouvoir vous connecter à cette base de données avec le portail de gestion pour la base de données SQL, Excel dans Office 365 ou Azure SQL Reporting.

    -   Une fois que vous avez terminé, cliquez sur la coche en bas de la page.

	Comme vous pouvez le remarquer, vous n'avez spécifié aucun nom de serveur. Dans la mesure où le serveur de la base de données SQL doit être accessible dans le monde entier, la base de données SQL configure les entrées DNS appropriées à la création du serveur. Le nom généré garantit l'absence de conflit avec d'autres entrées DNS. Vous ne pouvez pas changer le nom de votre serveur de base de données SQL.

    À l'étape suivante, vous allez configurer le pare-feu de telle sorte que les connexions des applications exécutées sur votre ordinateur soient autorisées à accéder aux bases de données sur votre serveur de base de données SQL.

Étape 3 : configuration du pare-feu
-----------------------------------

Pour configurer le pare-feu de telle sorte que les connexions puissent y transiter, vous allez entrer des informations sur la page du serveur.

**Remarque :** le service de base de données SQL est disponible uniquement avec le port TCP 1433 utilisé par le protocole TDS. Assurez-vous par conséquent que le pare-feu sur votre réseau et l'ordinateur local autorise les communications TCP sortantes sur le port 1433. Pour plus d'informations, consultez la page [Pare-feu de la base de données SQL](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-en-us.aspx).

1.  Dans le volet de navigation sur la gauche, cliquez sur **Bases de données SQL**.

2.  Cliquez sur **Serveurs** en haut de la page. Cliquez ensuite sur le serveur que vous venez de créer de manière à afficher une flèche blanche sur le côté droit. Cliquez sur la flèche pour ouvrir la page du serveur.

    ![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

3.  Sur la page du serveur, cliquez sur **Configurer** pour ouvrir les paramètres de configuration du pare-feu et spécifiez la règle comme suit :

	![Image6](./media/sql-database-get-started/6DBConfigFirewall_SQLTut.png)

	-   Copiez l’adresse IP actuelle du client. Il s'agit de l'adresse IP sur laquelle votre routeur ou serveur proxy est à l'écoute. La base de données SQL détecte l'adresse IP utilisée par la connexion actuelle afin que vous puissiez créer une règle de pare-feu pour accepter les demandes de connexion provenant de cet appareil.

	-   Collez l'adresse IP dans les plages de début et de fin. Plus tard, si vous êtes confronté à des erreurs de connexion indiquant que la plage est trop étroite, vous pourrez modifier cette règle pour élargir la plage.

	-   Entrez un nom pour la règle de pare-feu, par exemple, le nom de l'ordinateur ou de l'entreprise.

	-   Cliquez sur la coche pour enregistrer la règle.

    Une fois la règle enregistrée, votre page aura le même aspect que la capture d'écran suivante.

    ![Image7](./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png)

4.  Cliquez sur **Enregistrer** en bas de la page pour achever cette étape. Si **Enregistrer** n'est pas visible, actualisez la page du navigateur.

Vous avez à présent un serveur de base de données SQL sur Azure, une règle de pare-feu qui permet l'accès au serveur, un objet de base de données et une connexion administrateur. Vous allez ensuite exécuter un script Transact-SQL afin de créer une base de données prédéfinie en utilisant la fenêtre de requête dans le portail de gestion pour la base de données SQL.

Au fur et à mesure que vous développerez vos compétences, vous aurez envie d'explorer d'autres méthodes de création de bases de données, notamment les approches par programme ou le concepteur disponible dans SQL Server Data Tools. Si une base de données SQL Server existante est déjà exécutée sur un serveur local, vous pouvez la faire migrer facilement vers le serveur Azure que vous venez de configurer. Utilisez les liens à la fin de ce didacticiel pour savoir comment procéder.

Étape 4 : ajout de données et d'un schéma avec un script Transact-SQL
---------------------------------------------------------------------

Dans cette étape, vous allez exécuter deux scripts. Le premier crée un schéma qui définit des tables, des colonnes et des relations. Le second script ajoute les données. Chaque opération s'effectue indépendamment sur une connexion distincte. Si vous avez déjà créé des bases de données dans SQL Server, l'une des différences que vous pourrez constater dans la base de données SQL concerne les commandes CREATE et INSERT : elles doivent être exécutées dans des lots distincts. La base de données SQL impose cette exigence pour minimiser les attaques contre les données pendant leur transport.

**Remarque :** le schéma et les valeurs des données proviennent de cet [article MSDN](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee621790.aspx "article MSDN") et ont été modifiés pour fonctionner avec la base de données SQL.

1.  Accédez à la page d'accueil. Dans le [portail de gestion](http://manage.windowsazure.com), la seconde base de données **School** apparaît dans la liste des éléments sur la page d'accueil.

    ![Image8](./media/sql-database-get-started/8MainPageHome_SQLTut.png)

2.  Cliquez sur **School** de manière à afficher une flèche blanche sur le côté droit. Cliquez sur la flèche pour ouvrir la page de la base de données.

    ![Image9](./media/sql-database-get-started/9DBListSchool_SQLTut.png)

3.  Cliquez sur **Gérer** en bas de la page. Si ce bouton n'est pas visible, actualisez la fenêtre du navigateur. Le portail de gestion pour la base de données SQL s'ouvre. Ce portail est distinct du portail de gestion Azure. Vous allez utiliser ce portail pour exécuter les commandes et les requêtes Transact-SQL.

    ![Image10](./media/sql-database-get-started/10DBPortalManageButton_SQLTut.png)

4.  Entrez le nom et le mot de passe de connexion administrateur. Il s'agit de la connexion administrateur que vous avez spécifiée à la création du serveur.

    ![Image11](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)

5.  Cliquez sur **Nouvelle requête** dans le portail de gestion pour la base données SQL. Une fenêtre de requête vide s'ouvre dans l'espace de travail. À l'étape suivante, vous allez copier dans cette fenêtre une série de scripts prédéfinis qui ajouteront une structure et des données à votre base de données vide.

    ![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

Étape 5 : création du schéma
----------------------------

Vous allez à présent créer le schéma en utilisant le script suivant. Le script commence par rechercher s'il existe une table existante de même nom pour s'assurer qu'il n'y aura pas de conflit de nom, puis crée la table avec l'instruction [CREATE TABLE](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336258.aspx). Par ailleurs, ce script utilise l'instruction [ALTER TABLE](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336286.aspx) pour spécifier la clé primaire et les relations de la table.

Copiez le script et collez-le dans la fenêtre de requête. Cliquez sur **Exécuter** en haut de la fenêtre pour exécuter le script.

``` {data-morhtml="true"}
    -- Créer la table Department.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Department]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Department](
        [DepartmentID] [int] NOT NULL,
        [Name] [nvarchar](50) NOT NULL,
        [Budget] [money] NOT NULL,
        [StartDate] [datetime] NOT NULL,
        [Administrator] [int] NULL,
     CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED 
    (
    [DepartmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Créer la table Person.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Person]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Person](
        [PersonID] [int] IDENTITY(1,1) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [HireDate] [datetime] NULL,
        [EnrollmentDate] [datetime] NULL,
     CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED   
    (
    [PersonID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Créer la table OnsiteCourse.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OnsiteCourse](
        [CourseID] [int] NOT NULL,
        [Location] [nvarchar](50) NOT NULL,
        [Days] [nvarchar](50) NOT NULL,
        [Time] [smalldatetime] NOT NULL,
     CONSTRAINT [PK_OnsiteCourse] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Créer la table OnlineCourse.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OnlineCourse]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OnlineCourse](
        [CourseID] [int] NOT NULL,
        [URL] [nvarchar](100) NOT NULL,
     CONSTRAINT [PK_OnlineCourse] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Créer la table StudentGrade.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[StudentGrade](
        [EnrollmentID] [int] IDENTITY(1,1) NOT NULL,
        [CourseID] [int] NOT NULL,
        [StudentID] [int] NOT NULL,
        [Grade] [decimal](3, 2) NULL,
     CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED 
    (
        [EnrollmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Créer la table CourseInstructor.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[CourseInstructor](
        [CourseID] [int] NOT NULL,
        [PersonID] [int] NOT NULL,
     CONSTRAINT [PK_CourseInstructor] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC,
        [PersonID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Créer la table Course.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Course]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Course](
        [CourseID] [int] NOT NULL,
        [Title] [nvarchar](100) NOT NULL,
        [Credits] [int] NOT NULL,
        [DepartmentID] [int] NOT NULL,
     CONSTRAINT [PK_School.Course] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Créer la table OfficeAssignment.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OfficeAssignment](
        [InstructorID] [int] NOT NULL,
        [Location] [nvarchar](50) NOT NULL,
        [Timestamp] [timestamp] NOT NULL,
     CONSTRAINT [PK_OfficeAssignment] PRIMARY KEY CLUSTERED 
    (
        [InstructorID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Définir la relation entre OnsiteCourse et Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
    ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[OnsiteCourse] CHECK 
       CONSTRAINT [FK_OnsiteCourse_Course];
    GO

    -- Définir la relation entre OnlineCourse et Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
    ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[OnlineCourse] CHECK 
       CONSTRAINT [FK_OnlineCourse_Course];
    GO
    -- Définir la relation entre StudentGrade et Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
    ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Course];
    GO

    -- Définir la relation entre StudentGrade et Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))   
    ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Student];
    GO

    -- Définir la relation entre CourseInstructor et Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
     WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
     AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
     CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[CourseInstructor] CHECK 
      CONSTRAINT [FK_CourseInstructor_Course];
    GO

    -- Définir la relation entre CourseInstructor et Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
      WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
      CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[CourseInstructor] CHECK 
     CONSTRAINT [FK_CourseInstructor_Person];
    GO

    -- Définir la relation entre Course et Department.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
    ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
    REFERENCES [dbo].[Department] ([DepartmentID]);
    GO
    ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
    GO

    -- Définir la relation entre OfficeAssignment et Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
      WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
      AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
    ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  
      CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[OfficeAssignment] CHECK 
     CONSTRAINT [FK_OfficeAssignment_Person];
    GO
```

Étape 6 : Insertion des données
-------------------------------

Ouvrez une nouvelle fenêtre de requête, puis collez-y le script suivant. Exécutez le script pour insérer les données. Ce script utilise l'instruction [INSERT](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336284.aspx) pour ajouter des valeurs à chaque colonne.

``` {data-morhtml="true"}
    -- Insérer des données dans la table Person.
    SET IDENTITY_INSERT dbo.Person ON;
    GO
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (1, 'Abercrombie', 'Kim', '1995-03-11', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (2, 'Barzdukas', 'Gytis', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (3, 'Justice', 'Peggy', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (4, 'Fakhouri', 'Fadi', '2002-08-06', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (5, 'Harui', 'Roger', '1998-07-01', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (6, 'Li', 'Yan', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (7, 'Norman', 'Laura', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (8, 'Olivotto', 'Nino', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (9, 'Tang', 'Wayne', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (10, 'Alonso', 'Meredith', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (11, 'Lopez', 'Sophia', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (12, 'Browning', 'Meredith', null, '2000-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (13, 'Anand', 'Arturo', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (14, 'Walker', 'Alexandra', null, '2000-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (15, 'Powell', 'Carson', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (16, 'Jai', 'Damien', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (17, 'Carlson', 'Robyn', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (18, 'Zheng', 'Roger', '2004-02-12', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (19, 'Bryant', 'Carson', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (20, 'Suarez', 'Robyn', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (21, 'Holt', 'Roger', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (22, 'Alexander', 'Carson', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (23, 'Morgan', 'Isaiah', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (24, 'Martin', 'Randall', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (25, 'Kapoor', 'Candace', '2001-01-15', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (26, 'Rogers', 'Cody', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (27, 'Serrano', 'Stacy', '1999-06-01', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (28, 'White', 'Anthony', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (29, 'Griffin', 'Rachel', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (30, 'Shan', 'Alicia', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (31, 'Stewart', 'Jasmine', '1997-10-12', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (32, 'Xu', 'Kristen', '2001-7-23', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (33, 'Gao', 'Erica', null, '2003-01-30');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (34, 'Van Houten', 'Roger', '2000-12-07', null);
    GO
    SET IDENTITY_INSERT dbo.Person ON;
    GO
    -- Insérer des données dans la table Department.
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (2, 'English', 120000.00, '2007-09-01', 6);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
    GO
    -- Insérer des données dans la table Course.
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (1050, 'Chemistry', 4, 1);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (1061, 'Physics', 4, 1);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (1045, 'Calculus', 4, 7);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (2030, 'Poetry', 2, 2);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (2021, 'Composition', 3, 2);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (2042, 'Literature', 4, 2);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (4022, 'Microeconomics', 3, 4);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (4041, 'Macroeconomics', 3, 4);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (4061, 'Quantitative', 2, 4);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (3141, 'Calculus', 4, 7);
    GO
    -- Insérer des données dans la table OnlineCourse.
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (2030, 'http://www.fineartschool.net/Poetry');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (2021, 'http://www.fineartschool.net/Composition');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
    --Insérer des données dans la table OnsiteCourse.
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (1050, '123 Smith', 'MTWH', '11:30');
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (1061, '234 Smith', 'TWHF', '13:15');
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (1045, '121 Smith','MWHF', '15:30');
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (4061, '22 Williams', 'TH', '11:15');
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (2042, '225 Adams', 'MTWH', '11:00');
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (4022, '23 Williams', 'MWF', '9:00');
    -- Insérer des données dans la table CourseInstructor.
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (1050, 1);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (1061, 31);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (1045, 5);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (2030, 4);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (2021, 27);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (2042, 25);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (4022, 18);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (4041, 32);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (4061, 34);
    GO
    -- Insérer des données dans la table OfficeAssignment.
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (1, '17 Smith');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (4, '29 Adams');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (5, '37 Williams');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (18, '143 Smith');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (25, '57 Adams');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (27, '271 Williams');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (31, '131 Smith');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (32, '203 Williams');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (34, '213 Smith');
    -- Insérer des données dans la table StudentGrade.
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 2, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2030, 2, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 3, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2030, 3, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 6, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2042, 6, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 7, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2042, 7, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 8, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2042, 8, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 9, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 10, null);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 11, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 12, null);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 12, null);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 14, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 13, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 13, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 14, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 15, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 16, 2);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 17, null);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 19, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 20, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 21, 2);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 22, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 22, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 22, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 23, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1045, 23, 1,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 24, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 25, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 26, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 26, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 27, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1045, 28, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 28, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 29, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 30, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 30, 4);
    GO
```

Étape 7 : interrogation des données de l'exemple et des données système dans le portail de gestion pour la base de données SQL
------------------------------------------------------------------------------------------------------------------------------

Pour contrôler votre travail, exécutez une requête qui renvoie les données que vous venez d'entrer. Vous pouvez également exécuter des procédures stockées intégrées et des vues de gestion des données qui fournissent des informations sur les bases de données en cours d'exécution sur votre serveur de base de données SQL.

#### Interrogation des données de l'exemple

Dans une nouvelle fenêtre de requête, copiez et exécutez le script Transact-SQL suivant pour récupérer les données que vous venez d'ajouter.

``` {data-morhtml="true"}
    SELECT
        Course.Title as "Course Title"
        ,Department.Name as "Department"
        ,Person.LastName as "Instructor"
        ,OnsiteCourse.Location as "Location"
        ,OnsiteCourse.Days as "Days"
        ,OnsiteCourse.Time as "Time"
    FROM
     Course
     INNER JOIN Department
      ON Course.DepartmentID = Department.DepartmentID
     INNER JOIN CourseInstructor
       ON Course.CourseID = CourseInstructor.CourseID
     INNER JOIN Person
       ON CourseInstructor.PersonID = Person.PersonID
     INNER JOIN OnsiteCourse
        ON OnsiteCourse.CourseID = CourseInstructor.CourseID;
```

Vous obtenez un jeu de résultats similaire à celui représenté dans l'illustration suivante.

![Image13](./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG)

#### Interrogation des données système

Vous pouvez également utiliser des vues système et des procédures stockées intégrées pour obtenir des informations du serveur. Pour les besoins de ce didacticiel, vous allez simplement tester quelques commandes.

Exécutez la commande suivante pour déterminer quelles sont les bases de données disponibles sur le serveur.

    SELECT * FROM sys.databases  

Exécutez cette commande pour renvoyer la liste des utilisateurs actuellement connectés au serveur.

    SELECT user_name(),suser_sname()

Exécutez cette procédure stockée pour renvoyer la liste de tous les objets dans la base de données **École**.

    EXEC SP_help

Ne fermez pas la connexion du portail à la base de données **École**. Vous en aurez à nouveau besoin dans quelques minutes.

Étape 8 : création d'une connexion de base de données et attribution d'autorisations
------------------------------------------------------------------------------------

Dans la base de données SQL, vous pouvez créer des informations de connexion et attribuer des autorisations avec Transact-SQL. Dans cette leçon, vous allez exécuter trois opérations avec Transact-SQL : création d'une connexion d'authentification SQL Server, création d'un utilisateur de base de données et attribution d'autorisations avec l'appartenance à un rôle.

Une connexion d'authentification SQL Server est utilisée pour les connexions au serveur. Tous les utilisateurs qui accèdent à une base de données sur un serveur de base de données SQL le font en fournissant un nom et un mot de passe de connexion d'authentification SQL Server.

Pour créer une connexion, vous devez d'abord vous connecter à la base de données principale (**master**).

#### Création d'une connexion d'authentification SQL Server

1.  Dans le [portail de gestion](http://manage.windowsazure.com), sélectionnez **Bases de données SQL**, cliquez sur **Serveurs**, choisissez le serveur, puis cliquez sur la flèche blanche pour ouvrir la page du serveur.

	![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

2.  Sur la page Démarrage rapide, cliquez sur **Gérer le serveur** afin d'ouvrir une nouvelle connexion au portail de gestion pour la base de données SQL.

3.  Entrez le nom et le mot de passe de l'administrateur. Il s'agit de la connexion administrateur que vous avez spécifiée à la création du serveur.

	![Image20](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)

4.  Le portail de gestion de la base de données SQL s'ouvre dans une nouvelle fenêtre de navigateur. Cliquez sur **Sélectionner une base de données** en haut de la fenêtre, puis cliquez sur **master**.

    ![Image14](./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG)

5.  Si une erreur similaire à celle affichée ci-dessous apparaît sur la page, ignorez-la. Cliquez sur **Nouvelle requête** pour ouvrir une fenêtre de requête vous permettant d'exécuter des commandes Transact-SQL sur la base de données principale (**master**).

    ![Image15](./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG)

6.  Copiez et collez la commande suivante dans la fenêtre de requête.

         CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  Exécutez la commande suivante pour créer une connexion SQL Server nommée « SQLDBLogin ».

#### Création d'un utilisateur de base de données et attribution d'autorisations

Une fois que vous avez créé une connexion d'authentification SQL, l'étape suivante consiste à attribuer les niveaux de base de données et d'autorisation associés. À cet effet, vous créez un **utilisateur de base de données** sur chaque base de données.

1.  Revenez à la page du portail de gestion de la base de données SQL qui permet de se connecter à la base de données **School**. Si vous avez fermé la fenêtre du navigateur, démarrez une nouvelle connexion à la base de données **School** en suivant la procédure décrite dans la leçon précédente, « Ajout de données et d'un schéma avec un script Transact-SQL ».

    Sur la page du portail de gestion de la base de données SQL, le nom de la base de données **School** est visible dans le coin supérieur gauche.

    ![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

2.  Cliquez sur **Nouvelle requête** pour ouvrir une nouvelle fenêtre de requête, puis copiez dans cette dernière l'instruction suivante.

         CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  Exécutez le script. Ce script crée un utilisateur de base de données sur la connexion.

    Vous allez ensuite attribuer des autorisations avec le rôle db\_datareader. Les utilisateurs de base de données attribués à ce rôle peuvent lire l'intégralité des données de toutes les tables utilisateur dans la base de données.

4.  Ouvrez une nouvelle fenêtre de requête, puis entrez et exécutez l'instruction suivante. Cette instruction exécute une procédure stockée intégrée qui attribue le rôle db\_datareader au nouvel utilisateur que vous venez de créer.

         EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

    Vous disposez à présent d'une nouvelle connexion d'authentification SQL Server qui possède une autorisation d'accès en lecture seule à la base de données **School**. Cette procédure vous permet de créer d'autres connexions d'authentification SQL Server pour autoriser différents niveaux d'accès à vos données.

Étape 9 : connexion depuis d'autres applications
------------------------------------------------

Maintenant que vous avez une base de données opérationnelle, vous pouvez vous y connecter à partir d'un classeur Excel.

#### Connexion à partir d'Excel

Si Excel 2010 est installé sur votre ordinateur, vous pouvez utiliser la procédure suivante pour vous connecter à votre exemple de base de données.

1.  Dans Excel, sous l'onglet Données, cliquez sur **À partir d'autres sources**, puis cliquez sur **À partir de SQL Server**.

2.  Dans l'Assistant Connexion de données, entrez le nom de domaine complet de votre serveur de base de données SQL, suivi d'une connexion d'authentification SQL Server munie de l'autorisation d'accès à la base de données.

	Le nom du serveur se trouve sur la page **Base de données**, sous **Liens rapides**. Vous pouvez également trouver le nom du serveur sur le portail de gestion Azure, sur la base de données SQL, sur la page du serveur, sur le tableau de bord, dans **Manage URL**.

	Le nom du serveur est constitué d'une série de lettres et de chiffres, suivie de « .database.windows.net ». Indiquez ce nom dans l'Assistant Connecteur de base de données, sans inclure le préfixe http:// ou https://.

	Entrez une connexion d'authentification SQL Server. À des fins de test, vous pouvez utiliser la connexion administrateur que vous avez créée lors de la configuration du serveur. Pour l'accès régulier aux données, utilisez une connexion utilisateur de base de données similaire à celle que vous venez de créer.

	![Image16](./media/sql-database-get-started/16ExcelConnect_SQLTut.png)

3.  Sur la page suivante, choisissez la base de données **School**, puis **Course**. Cliquez sur **Terminer**.

	![Image17](./media/sql-database-get-started/17ExcelSelect_SQLTut.png)

4.  La boîte de dialogue Importer des données s'affiche et vous invite à choisir comment et où vous souhaitez importer vos données. Les options par défaut étant sélectionnées, cliquez sur **OK**.

    ![Image19](./media/sql-database-get-started/19ExcelImport_SQLTut.png)

3.  Dans la feuille de calcul, un tableau similaire au suivant doit apparaître.

    ![Image18](./media/sql-database-get-started/18ExcelTable_SQLTut.PNG)

Avec Excel seul, vous ne pouvez importer qu'une seule table à la fois. Une meilleure approche consiste à utiliser le complément PowerPivot pour Excel, qui vous permet d'importer et d'utiliser plusieurs tables comme un seul jeu de données. L'utilisation de PowerPivot n'entre pas dans le cadre de ce didacticiel, mais vous pouvez obtenir plus d'informations sur ce [site Web Microsoft](http://www.microsoft.com/en-us/bi/powerpivot.aspx).

Étape 10 : configuration de la synchronisation des données SQL
--------------------------------------------------------------

#### Synchronisation des données SQL

Maintenant que vous avez créé vos instances de base de données SQL, vous pouvez tirer parti de la synchronisation des données SQL pour maintenir la synchronisation de vos données à forte valeur ajoutée entre plusieurs emplacements.

La synchronisation des données SQL est une fonction de la base de données SQL qui vous permet de synchroniser une sélection de données, soit en fonction d'un calendrier soit à la demande, sans écrire de code ni de script. La synchronisation des données SQL prend en charge la synchronisation entre des instances de la base de données SQL ou des topologies hybrides incluant des bases de données SQL et des instances de SQL Server.

Pour plus d'informations sur la synchronisation des données SQL, consultez la page [Prise en main de la synchronisation des données SQL](http://go.microsoft.com/fwlink/?LinkId=274959).

Étapes suivantes
----------------

À présent que vous êtes familiarisé avec la base de données SQL et les portails de gestion, vous pouvez essayer d'autres outils et techniques employés par les administrateurs de bases de données SQL Server.

Pour gérer activement votre nouvelle base de données, songez à installer et utiliser SQL Server Management Studio. Management Studio est le principal outil d'administration de base de données pour gérer les bases de données SQL Server, y compris celles qui s'exécutent sur Azure. Avec Management Studio, vous pouvez enregistrer les requêtes pour un usage ultérieur, ajouter de nouvelles tables et procédures stockées, et aiguiser vos compétences relatives à Transact-SQL dans un environnement enrichi de création de scripts, qui inclut un vérificateur de syntaxe, Intellisense et des modèles. Pour la prise en main, suivez les instructions décrites dans la page [Gestion des bases de données SQL avec SQL Server Management Studio](http://www.windowsazure.com/fr-fr/develop/net/common-tasks/sql-azure-management/).

Une bonne maîtrise du langage de définition des requêtes et des données Transact-SQL est essentielle pour les administrateurs de base de données. Si vous débutez avec Transact-SQL, commencez par le [didacticiel : écriture d'instructions Transact-SQL](http://msdn.microsoft.com/fr-fr/library/ms365303.aspx) pour apprendre quelques techniques de base.

Il existe d'autres méthodes pour transférer une base de données locale vers la base de données SQL. Si vous possédez déjà des bases de données ou si vous avez téléchargé des exemples de base de données pour vous entraîner, essayez les autres approches suivantes :

-   [Migration de bases de données vers la base de données SQL](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee730904.aspx)
-   [Copie de bases de données dans la base de données SQL](http://msdn.microsoft.com/fr-fr/library/windowsazure/ff951624.aspx)

