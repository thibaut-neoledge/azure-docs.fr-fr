

Gestion des bases de données SQL sur Azure
==========================================

Ce guide vous montre comment effectuer des tâches d'administration pour des serveurs logiques et des instances de base de données sur la base de données SQL Azure.

Définition de la base de données SQL
------------------------------------

La base de données SQL fournit des services de gestion des bases de données relationnelles sur Azure. Elle est fondée sur la technologie SQL Server. La base de données SQL permet d'approvisionner et de déployer facilement des instances de bases de données. Elle tire parti d'un centre de données distribué qui fournit haute disponibilité, extensibilité et sécurité aux entreprises, avec les avantages de la protection des données et de l'auto-adaptation intégrées.

Sommaire
--------

-   [Connexion à Azure](#PreReq1)
-   [Configuration de la base de données SQL](#PreReq2)
-   [Connexion à l'aide de Management Studio](#PreReq3)
-   [Déploiement d'une base de données dans Azure](#HowTo1)
-   [Ajout de connexions et d'utilisateurs](#HowTo2)
-   [Mise à l'échelle d'une solution de base de données SQL](#HowTo4)
-   [Surveillance des serveurs logiques et des instances de base de données](#HowTo3)
-   [Étapes suivantes](#NextSteps)

Connexion à Azure
-----------------

La base de données SQL fournit des services de stockage de données relationnelles, d'accès aux données et de gestion sur Azure. Pour utiliser ces services, vous avez besoin d'un abonnement Azure.

1.  Ouvrez un navigateur Web et accédez à la page <http://www.windowsazure.com>. Pour commencer avec un compte gratuit, cliquez sur Version d'évaluation gratuite dans le coin supérieur droit, puis suivez la procédure.

2.  Votre compte est maintenant créé. Vous êtes prêt à commencer.

Création et configuration d'une base de données SQL
---------------------------------------------------

Ensuite, vous allez découvrir la création et la configuration d'un serveur logique. La nouvelle version préliminaire du portail de gestion Azure propose une refonte des workflows qui vous permettent de commencer par la création d'une base de données avant de créer un serveur.

Dans ce guide, nous allons commencer par la création d'un serveur. Vous pouvez privilégier cette méthode si vous souhaitez télécharger des bases de données SQL Server existantes.

### Création d'un serveur logique

1.  Connectez-vous à <http://www.windowsazure.com>,

2.  Cliquez sur **Base de données SQL**, puis sur **SERVEURS** sur la page d'accueil de la base de données SQL.

3.  Cliquez sur **Ajouter** en bas de la page.

4.  Sur la page Paramètres du serveur, entrez un nom d'administrateur en un mot (sans espace).

    La base de données SQL utilise l'authentification SQL sur une connexion chiffrée. Une nouvelle connexion d'authentification SQL Server attribuée au rôle serveur administrateur système fixe est créée avec le nom indiqué.

    L'identifiant de connexion ne peut pas correspondre à une adresse électronique, à un compte d'utilisateur Windows, ni à un Windows Live ID. Les revendications et l'authentification Windows ne sont pas prises en charge sur la base de données SQL.

5.  Fournissez un mot de passe fort de plus de huit caractères, combinant des majuscules et des minuscules, et comportant un chiffre ou un symbole.

6.  Choisissez une région. La région détermine l'emplacement géographique du serveur. Choisissez une région qui est pertinente pour ce serveur, car il n'est pas possible de basculer facilement d'une région à l'autre. Choisissez un emplacement le plus proche possible de vous. Vous économisez sur les coûts de la bande passante de sortie et réduisez la latence des données en maintenant l'application et la base de données Azure dans la même région.

7.  Veillez à ce que l'option **Autoriser les services** reste activée pour pouvoir vous connecter à cette base de données avec le portail de gestion pour la base de données SQL, les services de stockage et d'autres services sur Azure.

8.  Une fois que vous avez terminé, cliquez sur la coche en bas de la page.

Comme vous pouvez le remarquer, vous n'avez spécifié aucun nom de serveur. La base de données SQL génère automatiquement le nom du serveur pour garantir l'absence d'entrées DNS en double. Le nom du serveur se présente sous la forme d'une chaîne alphanumérique de dix caractères. Vous ne pouvez pas changer le nom de votre serveur de base de données SQL.

À l'étape suivante, vous allez configurer le pare-feu de telle sorte que les connexions des applications exécutées sur votre réseau soient autorisées à y accéder.

### Configuration du pare-feu pour le serveur logique

1.  Cliquez sur **Bases de données SQL**, sur **Serveurs**, puis sur le serveur que vous venez de créer.

2.  Cliquez sur **Configurer**.

3.  Copiez l'adresse IP actuelle du client. Si vous vous connectez depuis un réseau, il s'agit de l'adresse IP sur laquelle votre routeur ou serveur proxy est à l'écoute. La base de données SQL détecte l'adresse IP utilisée par la connexion actuelle afin que vous puissiez créer une règle de pare-feu pour accepter les demandes de connexion provenant de cet appareil.

4.  Collez l'adresse IP dans les plages de début et de fin. Plus tard, si vous êtes confronté à des erreurs de connexion indiquant que la plage est trop étroite, vous pourrez modifier cette règle pour élargir la plage.

    Si les ordinateurs clients utilisent des adresses IP attribuées dynamiquement, vous devez indiquer une plage suffisamment large afin d'inclure les adresses IP attribuées aux ordinateurs de votre réseau. Optez pour une plage restreinte au départ, puis étendez-la selon vos besoins.

5.  Entrez un nom pour la règle de pare-feu, par exemple, le nom de l'ordinateur ou de l'entreprise.

6.  Cliquez sur la coche pour enregistrer la règle.

7.  Cliquez sur **Enregistrer** en bas de la page pour achever cette étape. Si **Enregistrer** n'est pas visible, actualisez la page du navigateur.

Vous disposez à présent d'un serveur logique, d'une règle de pare-feu qui autorise les connexions entrantes provenant de votre adresse IP et d'une connexion administrateur. À l'étape suivante, repassez sur votre ordinateur local afin d'effectuer les étapes de configuration restantes.

**Remarque :** le serveur logique que vous venez de créer est temporaire et hébergé dynamiquement sur plusieurs serveurs physiques d'un centre de données. Si vous supprimez le serveur, sachez qu'il s'agit d'une action irréversible. Veillez à sauvegarder toutes les bases de données que vous téléchargez par la suite sur le serveur.

Connexion à l'aide de Management Studio
---------------------------------------

Management Studio est un outil d'administration qui permet de gérer plusieurs serveurs et instances SQL Server dans un espace de travail unique. Si vous disposez déjà d'une instance SQL Server locale, vous pouvez ouvrir une connexion sur une instance locale et un serveur logique sur Azure afin d'effectuer des tâches côte à côte.

Management Studio comprend des fonctionnalités qui ne sont actuellement pas disponibles sur le portail de gestion, telles qu'un vérificateur de syntaxe et la capacité d'enregistrement de scripts et des requêtes nommées à des fins de réutilisation. La base de données SQL est simplement un point de terminaison TDS (Tabular Data Stream). Tous les outils qui fonctionnent avec TDS, notamment Management Studio, sont valides pour les opérations relatives à la base de données SQL. Les scripts que vous développez pour le serveur local s'exécuteront sur un serveur logique de la base de données SQL.

À l'étape suivante, vous allez utiliser Management Studio pour vous connecter à un serveur logique sur Azure. Cette étape implique que vous disposiez de SQL Server Management Studio version 2008 R2 ou 2012. Afin d'obtenir de l'aide pour télécharger Management Studio ou vous y connecter, consultez la page [Gestion de la base de données SQL à l'aide de Management Studio](http://www.windowsazure.com/en-us/develop/net/common-tasks/sql-azure-management/) sur ce site.

Avant de pouvoir vous connecter, il est parfois nécessaire de créer une exception de pare-feu qui autorise des requêtes sortantes sur le port 1433 sur votre système local. Le port 1433 des ordinateurs qui sont sécurisés par défaut n'est généralement pas ouvert.

### Configuration du pare-feu pour le serveur local

1.  Dans Pare-feu Windows avec fonctions avancées de sécurité, créez une règle sortante.

2.  Choisissez **Port**, spécifiez TCP 1433, indiquez **Autoriser la connexion** et vérifiez que le profil **Public** est sélectionné.

3.  Indiquez un nom explicite, tel que *WindowsAzureSQLDatabase (tcp-out) port 1433*.

### Création d'un serveur logique

1.  Dans Management Studio et Se connecter au serveur, vérifiez que le moteur de base de données est sélectionné, entrez le nom du serveur logique au format suivant : *nom\_serveur*.database.widnows.net

    Vous pouvez également obtenir le nom de serveur complet dans le portail de gestion, sur le tableau de bord du serveur et dans GÉRER L'URL.

2.  Dans Authentification, choisissez **Authentification SQL Server**, puis entrez la connexion administrateur que vous avez créée lorsque vous avez configuré le serveur logique.

3.  Cliquez sur **Options**.

4.  Dans Connexion à une base de données, spécifiez **master**.

### Connexion à un serveur local

1.  Dans Management Studio et Se connecter au serveur, vérifiez que le moteur de base de données est sélectionné, entrez le nom d'une instance locale au format suivant : *nom\_serveur*\\*nom\_instance*. Si le serveur est local et une instance par défaut, entrez *localhost*.

2.  Dans Authentification, choisissez **Authentification Windows**, puis entrez un compte Windows qui est membre du rôle administrateur système.

Déploiement d'une base de données dans Azure
--------------------------------------------

Il existe de nombreuses approches pour déployer une base de données SQL Server locale dans Azure. Cette tâche implique l'utilisation de l'Assistant Déploiement d'une base de données vers SQL Azure pour télécharger un exemple de base de données.

L'exemple de base de données School est très simple et tous ses objets sont compatibles avec la base de données SQL. Il est donc inutile de modifier ou de préparer une base de données pour la migration. En tant que nouvel administrateur, déployez une première base de données simple pour découvrir les étapes impliquées avant d'utiliser vos propres bases de données.

**Remarque :** pour plus d'informations sur la préparation d'une base de données locale pour la migration dans Azure, consultez le guide de migration des bases de données SQL. Téléchargez également le kit de formation Azure. Une session est consacrée à une approche alternative de la migration d'une base de données locale.

### Création de la base de données School sur un serveur local

Les scripts pour la création de cette base de données sont disponibles dans la rubrique [Prise en main de l'administration de la base de données SQL](http://www.windowsazure.com/en-us/manage/tutorials/sql-azure-management/). Dans ce guide, vous allez exécuter ces scripts dans Management Studio pour créer une version locale de la base de données School.

1.  Dans Management Studio, connectez-vous à un serveur local. Cliquez avec le bouton droit sur **Bases de données**, cliquez sur **Nouvelle base de données**, puis entrez *school*.

2.  Cliquez avec le bouton droit sur *school*, puis cliquez sur **Nouvelle requête**.

3.  Copiez, puis exécutez le script Create Schema du didacticiel.

``` {}
    -- Créez la table Department.
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

    -- Créez la table Person.
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

    -- Créez la table OnsiteCourse.
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

    -- Créez la table OnlineCourse.
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

    -- Créez la table StudentGrade.
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

    -- Créez la table CourseInstructor.
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

    -- Créez la table Course.
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

    -- Créez la table OfficeAssignment.
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

    -- Définissez la relation entre OnsiteCourse et Course.
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

    -- Définissez la relation entre OnlineCourse et Course.
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

    -- Définissez la relation entre StudentGrade et Course.
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

    -- Définissez la relation entre StudentGrade et Course.
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

    -- Définissez la relation entre CourseInstructor et Course.
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

    -- Définissez la relation entre CourseInstructor et Person.
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

    -- Définissez la relation entre Course et Department.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
    ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
    REFERENCES [dbo].[Department] ([DepartmentID]);
    GO
    ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
    GO

    -- Définissez la relation entre OfficeAssignment et Person.
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

Copiez, puis exécutez le script Insert Data.

``` {}
    -- Insérez des données dans la table Person.
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
    -- Insérez des données dans la table Department.
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (2, 'English', 120000.00, '2007-09-01', 6);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
    GO
    -- Insérez des données dans la table Course.
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
    VALUES (3141, 'Trigonometry', 4, 7);
    GO
    -- Insérez des données dans la table OnlineCourse.
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (2030, 'http://www.fineartschool.net/Poetry');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (2021, 'http://www.fineartschool.net/Composition');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
    --Insérez des données dans la table OnsiteCourse.
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
    -- Insérez des données dans la table CourseInstructor.
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
    -- Insérez des données dans la table OfficeAssignment.
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
    -- Insérez des données dans la table StudentGrade.
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 2, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2030, 2, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 3, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2030, 3, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 6, 2.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2042, 6, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 7, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2042, 7, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 8, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2042, 8, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 9, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 10, null);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 11, 2.5);
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
    VALUES (4022, 15, 2.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 16, 2);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 17, null);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 19, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 20, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 21, 2);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 22, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 22, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 22, 2.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 23, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1045, 23, 1.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 24, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 25, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 26, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 26, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 27, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1045, 28, 2.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 28, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 29, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 30, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 30, 4);
    GO
```

Vous disposez maintenant d'une base de données locale que vous pouvez exporter dans Azure. Vous allez ensuite exécuter un Assistant qui crée un fichier .bacpac, le charge dans Azure et l'importe dans la base de données SQL.

### Déploiement sur la base de données SQL

1.  Dans Management Studio, connectez-vous à une instance SQL Server locale qui contient la base de données que vous souhaitez migrer.

2.  Cliquez avec le bouton droit sur la base de données School que vous venez de créer, pointez sur **Tâches**, puis cliquez sur **Déployer une base de données vers SQL Azure**.

3.  Dans Paramètres de déploiement, entrez un nom pour la base de données, par exemple *school*.

4.  Cliquez sur **Connexion**.

5.  Dans Nom du serveur, entrez les 10 caractères du nom du serveur, suivis de .database.windows.net.

6.  Dans la zone Authentification, choisissez **Authentification SQL Server**.

7.  Entrez le nom et le mot de passe de connexion administrateur que vous avez fournis lors de la création du serveur logique de base de données SQL.

8.  Cliquez sur **Options**.

9.  Dans Propriétés de connexion, dans Connexion à une base de données, tapez **master**.

10. Cliquez sur **Connexion**. Cette étape termine la spécification de la connexion et vous ramène à l'Assistant.

11. Cliquez sur **Suivant**, puis sur **Terminer** pour exécuter l'Assistant.

### Vérification du déploiement de la base de données

1.  Dans Management Studio, connectez-vous à un serveur logique. Si une connexion est déjà ouverte, vous pouvez la fermer et en ouvrir une autre. La connexion existante montre uniquement les bases de données qui étaient en cours d'exécution lorsque la connexion a été effectuée.

    Pour obtenir des instructions sur la connexion d'un serveur logique, consultez la rubrique [Connexion à l'aide de Management Studio](#PreReq3) dans ce document.

2.  Développez le dossier Bases de données. La base de données School doit s'afficher dans la liste.

3.  Cliquez avec le bouton droit sur la base de données School, puis cliquez sur **Nouvelle requête**.

4.  Exécutez la requête suivante pour vérifier que les données sont accessibles.

``` {}
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

Ajout de connexions et d'utilisateurs
-------------------------------------

Après avoir déployé une base de données, vous devez configurer des connexions et attribuer des autorisations. Dans cette étape, vous allez exécuter deux scripts.

Dans le premier script, vous allez vous connecter à master et exécuter un script qui crée des connexions. Les connexions seront utilisées pour prendre en charge les opérations de lecture et d'écriture, et la délégation de tâches opérationnelles, telles que la capacité à exécuter les requêtes système sans autorisation.

Les connexions que vous créez doivent être des connexions d'authentification SQL Server. Si vous disposez déjà des scripts qui utilisent des identités d'utilisateur Windows ou des identités basées sur les revendications, ils ne fonctionneront pas sur la base de données SQL.

Le second script attribue les autorisations aux utilisateurs de la base de données. Dans ce script, vous allez vous connecter à une base de données déjà chargée sur Azure.

### Création de connexions

1.  Dans Management Studio, connectez-vous à un serveur logique sur Azure, développez le dossier Bases de données, cliquez avec le bouton droit sur **master**, puis sélectionnez **Nouvelle requête**.

2.  Utilisez les instructions Transact-SQL suivantes pour créer des connexions. Remplacez le mot de passe par un mot de passe valide. Sélectionnez chacun d'eux, puis cliquez sur **Exécuter**. Répétez l'action précédente pour les connexions restantes.

``` {}
    -- exécuter sur master, exécuter chaque ligne individuellement
    -- utiliser cette connexion pour gérer d'autres connexions sur ce serveur
    CREATE LOGIN sqladmin WITH password='<ProvidePassword>'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- utiliser cette connexion pour créer ou copier une base de données
    CREATE LOGIN sqlops WITH password='<ProvidePassword>';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
```

### Création d'utilisateurs de base de données

1.  Développez le dossier Bases de données, cliquez avec le bouton droit sur **school**, puis sélectionnez **Nouvelle requête**.

2.  Utilisez les instructions Transact-SQL suivantes pour ajouter des utilisateurs de base de données. Remplacez le mot de passe par un mot de passe valide.

``` {}
    -- exécuter sur une base de données normale, exécuter chaque ligne séparément
    -- utiliser cette connexion pour les opérations de lecture
    CREATE LOGIN sqlreader WITH password='<ProvidePassword>';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- utiliser cette connexion pour les opérations d'écriture
    CREATE LOGIN sqlwriter WITH password='<ProvidePassword>';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- accorder des autorisations DMV sur la base de données school à 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
```

### Affichage et test de connexions

1.  Dans une nouvelle fenêtre de requête, connectez-vous à **master**, puis exécutez l'instruction suivante :

         SELECT * from sys.sql_logins;

2.  Dans Management Studio, cliquez avec le bouton droit sur la base de données **school**, puis sélectionnez **Nouvelle requête**.

3.  Dans le menu Requête, pointez sur **Connexion**, puis cliquez sur **Modifier la connexion**.

4.  Connectez-vous en tant que *sqlreader*.

5.  Copiez et essayez d'exécuter l'instruction suivante. Vous recevrez une erreur indiquant que l'objet n'existe pas.

         INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
         VALUES (1061, 30, 9);

6.  Ouvrez une seconde fenêtre de requête, puis remplacez le contexte de connexion par *sqlwriter*. La même requête doit à présent s'exécuter correctement.

Vous avez maintenant créé et testé plusieurs connexions. Pour plus d'informations, consultez les pages [Gestion des bases de données et des connexions dans Base de données SQL Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee336235.aspx) et [Contrôle de Base de données SQL Azure à l'aide de vues de gestion dynamique](http://msdn.microsoft.com/en-us/library/windowsazure/ff394114.aspx).

Surveillance des serveurs logiques et des instances de base de données
----------------------------------------------------------------------

Les outils et techniques de surveillance que vous avez peut-être l'habitude d'utiliser sur les serveurs locaux, tels que l'audit des identifiants de connexion, l'exécution de suivis et l'utilisation de compteurs de performances ne sont pas disponibles avec la base de données SQL. Sur Azure, vous utilisez les vues de gestion dynamique (DMV) pour surveiller la capacité des données, les problèmes liés aux requêtes et les connexions en cours.

Pour plus d'informations, consultez la page [Contrôle de Base de données SQL Windows Azure à l'aide de vues de gestion dynamique](http://msdn.microsoft.com/en-us/library/windowsazure/ff394114.aspx).

Mise à l'échelle d'une solution de base de données SQL
------------------------------------------------------

Sur Azure, l'extensibilité de la base de données est synonyme de montée en charge, où une charge de travail est redistribuée sur plusieurs serveurs de commodité d'un centre de données. La montée en charge est une stratégie visant à résoudre des problèmes de capacité ou de performances des données. Une base de données très volumineuse subissant une très forte croissance finira peut-être par nécessiter une stratégie de montée en charge, que peu d'utilisateurs y accèdent ou, au contraire, un très grand nombre.

Le meilleur moyen d'obtenir une montée en charge sur Azure est d'utiliser la fédération. La fédération de base de données SQL est basée sur le partitionnement horizontal, où une ou plusieurs tables sont fractionnées par ligne et réparties sur plusieurs membres de la fédération.

La fédération n'est pas la seule réponse à chaque problème d'extensibilité. Parfois, les caractéristiques des exigences de vos données ou applications pointent vers des approches plus simples. La liste suivante présente des solutions potentielles par ordre de complexité.

**Augmentation de la taille de la base de données**

Les bases de données sont créées à une taille fixe sujette à un maximum imposé par chaque édition. Pour une édition Web, vous pouvez augmenter une base de données jusqu'à un maximum de 5 gigaoctets. Pour une édition Business, la taille maximale de la base de données est de 150 gigaoctets. La manière la plus évidente d'augmenter la capacité de la base de données est de modifier l'édition et la taille maximale :

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

**Utilisation de plusieurs bases de données et allocation d'utilisateurs**

Dans les scénarios limités, vous pourriez créer des copies d'une base de données, puis allouer des connexions et des utilisateurs sur chaque base de données. Avant que la fédération ne soit une option, il s'agissait d'une approche courante pour redistribuer une charge de travail. Cette approche est viable pour les bases de données que vous utilisez à court terme, puis fusionnez ultérieurement dans une base de données principale que vous conservez localement, ainsi que pour les solutions qui fournissent des données en lecture seule.

**Utilisation de fédérations**

Les fédérations dans une base de données SQL sont utilisées pour obtenir une extensibilité et des performances accrues. Une ou plusieurs tables d'une base de données sont fractionnées par ligne et réparties sur plusieurs bases de données (membres de la fédération). Ce type de partitionnement horizontal est souvent nommé « sharding » (partitionnement). Les scénarios principaux dans lesquels ce partitionnement est utile sont ceux où vous devez atteindre une certaine extensibilité ou certaines performances, ou gérer la capacité.

Les fédérations sont prises en charge dans l'édition Business. Pour plus d'informations, consultez la page [Fédérations dans la base de données SQL](http://msdn.microsoft.com/en-us/library/windowsazure/hh597452.aspx) et [Didacticiel sur les fédérations de Base de données SQL -- DBA](http://msdn.microsoft.com/en-us/library/windowsazure/hh778416.aspx).

**Prise en considération d'autres formes de stockage**

Gardez à l'esprit qu'Azure prend en charge plusieurs formes de stockage des données, y compris des stockages de tables et d'objets blob. Si vous n'avez pas besoin de fonctionnalités relationnelles, le stockage des tables ou des objets blob peut être plus économique.

Étapes suivantes
----------------

Maintenant que vous avez appris les bases de l'administration de la base de données SQL, suivez ces liens pour effectuer des tâches d'administration plus complexes.

-   Consultez la page [Base de données SQL](http://msdn.microsoft.com/en-us/library/windowsazure/gg619386) sur MSDN
-   Accédez au [Wiki TechNet sur la base de données SQL](http://social.technet.microsoft.com/wiki/contents/articles/2267.sql-azure-technet-wiki-articles-index-en-us.aspx)

[Sign in to Azure]: #PreReq1
[Configure SQL Database]: #PreReq2
[Connect using Management Studio]: #PreReq3
[Deploy a database to Azure]: #HowTo1
[Add logins and users]: #HowTo2
[Monitor logical servers and database instances]: #HowTo3
[Scale a SQL Database solution]: #HowTo4
[Next Steps]: #NextSteps

[SQL Database]: http://msdn.microsoft.com/en-us/library/windowsazure/gg619386

[SQL Database TechNet WIKI]: http://social.technet.microsoft.com/wiki/contents/articles/2267.sql-azure-technet-wiki-articles-index-en-us.aspx

[How to Use SQL Database]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/sql-azure/
[Federations in SQL Database]: http://msdn.microsoft.com/en-us/library/windowsazure/hh597452.aspx
[SQL Database Federations Tutorial - DBA]: http://msdn.microsoft.com/en-us/library/windowsazure/hh778416.aspx
[Managing SQL Database using Management Studio]: http://www.windowsazure.com/en-us/develop/net/common-tasks/sql-azure-management/
[Monitoring SQL Database Using Dynamic Management Views]: http://msdn.microsoft.com/en-us/library/windowsazure/ff394114.aspx
[Introducing Geo-Replication for Windows Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
[How to create a storage account for an Azure Subscription]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433066.aspx
[Download Azure SDK]: http://www.microsoft.com/en-us/download/details.aspx?id=15658
[Azure Management Tools]: http://wapmmc.codeplex.com/
[Getting Started with SQL Database Administration]: http://www.windowsazure.com/en-us/manage/tutorials/sql-azure-management/  
[Managing Databases and Logins in SQL Database]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336235.aspx
[How to use the blob storage service]: https://www.windowsazure.com/en-us/develop/net/how-to-guides/blob-storage/
[DAC SQL Database Import Export Service Client v 1.5]: http://sqldacexamples.codeplex.com/releases/view/85948


[Adventure Works for SQL Database]: http://msftdbprodsamples.codeplex.com/releases/view/37304

