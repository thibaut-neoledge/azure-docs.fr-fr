<properties linkid="manage-services-how-to-deploy-a-sqldb" urlDisplayName="How to deploy" pageTitle="How to deploy a SQL Database - Azure" metaKeywords="" description="Learn how to deploy a SQL Server database to Azure. You will use the Deploy Database to SQL Database wizard to upload a sample database." metaCanonical="" services="sql-database" documentationCenter="" title="How to Deploy a Database to Azure" authors="Lori Clark" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Lori="" Clark" />

# <span id="howtodeploySQLdb"></span></a>Déploiement d'une base de données dans Azure

Il existe plusieurs façons de déployer une base de données SQL Server locale dans Azure. Cette tâche implique l'utilisation de l'Assistant Déploiement d'une base de données vers SQL Azure pour télécharger un exemple de base de données.

L'exemple de base de données School est très simple et tous ses objets sont compatibles avec la base de données SQL. Il est donc inutile de modifier ou de préparer une base de données pour la migration. En tant que nouvel administrateur, déployez une première base de données simple pour découvrir les étapes impliquées avant d'utiliser vos propres bases de données.

**Remarque :** pour plus d'informations sur la préparation d'une base de données locale pour la migration dans Azure, consultez le guide de migration des bases de données SQL. Téléchargez également le kit de formation Azure. Une session est consacrée à une approche alternative de la migration d'une base de données locale.

## Sommaire

-   [Création de la base de données School sur un serveur local][Création de la base de données School sur un serveur local]
-   [Déploiement dans la base de données SQL][Déploiement dans la base de données SQL]
-   [Vérification du déploiement de la base de données][Vérification du déploiement de la base de données]

## <span id="schooldb"></span></a> Création de la base de données School sur un serveur local

Les scripts pour la création de cette base de données sont disponibles dans la rubrique [Prise en main de l'administration de la base de données SQL][Prise en main de l'administration de la base de données SQL]. Dans ce guide, vous allez exécuter ces scripts dans Management Studio pour créer une version locale de la base de données School.

1.  Dans Management Studio, connectez-vous à un serveur local. Cliquez avec le bouton droit sur **Bases de données**, cliquez sur **Nouvelle base de données**, puis entrez *school*.

2.  Cliquez avec le bouton droit sur *school*, puis cliquez sur **Nouvelle requête**.

3.  Copiez, puis exécutez le script Create Schema du didacticiel.

<div style="width:auto; height:300px; overflow:auto"><pre>
    -- Cr&eacute;ez la table Department.
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

    -- Cr&eacute;ez la table Person.
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

    -- Cr&eacute;ez la table OnsiteCourse.
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

    -- Cr&eacute;ez la table OnlineCourse.
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

    -- Cr&eacute;ez la table StudentGrade.
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

    -- Cr&eacute;ez la table CourseInstructor.
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

    -- Cr&eacute;ez la table Course.
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

    -- Cr&eacute;ez la table OfficeAssignment.
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

    -- D&eacute;finissez la relation entre OnsiteCourse et Course.
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

    -- D&eacute;finissez la relation entre OnlineCourse et Course.
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
    -- D&eacute;finissez la relation entre StudentGrade et Course.
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

    -- D&eacute;finissez la relation entre StudentGrade et Course.
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

    -- D&eacute;finissez la relation entre CourseInstructor et Course.
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

    -- D&eacute;finissez la relation entre CourseInstructor et Person.
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

    -- D&eacute;finissez la relation entre Course et Department.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
    ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
    REFERENCES [dbo].[Department] ([DepartmentID]);
    GO
    ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
    GO

    -- D&eacute;finissez la relation entre OfficeAssignment et Person.
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
</pre></div>

Copiez, puis exécutez le script Insert Data.

<div style="width:auto; height:300px; overflow:auto"><pre>
    -- Ins&eacute;rez des donn&eacute;es dans la table Person.
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
    -- Ins&eacute;rez des donn&eacute;es dans la table Department.
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (2, 'English', 120000.00, '2007-09-01', 6);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
    GO
    -- Ins&eacute;rez des donn&eacute;es dans la table Course.
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
    -- Ins&eacute;rez des donn&eacute;es dans la table OnlineCourse.
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (2030, 'http://www.fineartschool.net/Poetry');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (2021, 'http://www.fineartschool.net/Composition');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
    --Ins&eacute;rez des donn&eacute;es dans la table OnsiteCourse.
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
    -- Ins&eacute;rez des donn&eacute;es dans la table CourseInstructor.
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
    -- Ins&eacute;rez des donn&eacute;es dans la table OfficeAssignment.
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
    -- Ins&eacute;rez des donn&eacute;es dans la table StudentGrade.
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 2, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2030, 2, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 3, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2030, 3, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 6, 2,5);
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
    VALUES (1050, 26, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 26, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 27, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1045, 28, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 28, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 29, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 30, 3.5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 30, 4);
    GO
</pre></div>

Vous disposez maintenant d'une base de données locale que vous pouvez exporter dans Azure. Vous allez ensuite exécuter un Assistant qui crée un fichier .bacpac, le charge dans Azure et l'importe dans la base de données SQL.

## <span id="deploydb"></span></a> Déploiement dans la base de données SQL

1.  Dans Management Studio, connectez-vous à une instance SQL Server locale qui contient la base de données que vous souhaitez migrer.

2.  Cliquez avec le bouton droit sur la base de données School que vous venez de créer, pointez sur **Tasks** et cliquez sur **Deploy Database to SQL Azure**.

3.  Dans Paramètres de déploiement, entrez un nom pour la base de données, par exemple *school*.

4.  Cliquez sur **Connecter**.

5.  Dans Nom du serveur, entrez les 10 caractères du nom du serveur, suivis de .database.windows.net.

6.  Dans la zone Authentification, choisissez **Authentification SQL Server**.

7.  Entrez le nom et le mot de passe de connexion administrateur que vous avez fournis lors de la création du serveur logique de base de données SQL.

8.  Cliquez sur **Options**.

9.  Dans Propriétés de connexion, dans Connexion à une base de données, tapez **master**.

10. Cliquez sur **Connecter**. Cette étape termine la spécification de la connexion et vous ramène à l'Assistant.

11. Cliquez sur **Suivant**, puis sur **Terminer** pour exécuter l'Assistant.

## <span id="verify"></span></a> Vérification du déploiement de la base de données

1.  Dans Management Studio, dans l'Explorateur d'objets, actualisez les bases de données pour afficher celle que vous venez de créer.

2.  Développez le dossier Bases de données. La base de données **School** doit s'afficher dans la liste.

3.  Cliquez avec le bouton droit sur la base de données School, puis cliquez sur **Nouvelle requête**.

4.  Exécutez la requête suivante pour vérifier que les données sont accessibles.

<div style="width:auto; height:auto; overflow:auto"><pre>
    SELECT
        Course.Title as &quot;Course Title&quot;
        ,Department.Name as &quot;Department&quot;
        ,Person.LastName as &quot;Instructor&quot;
        ,OnsiteCourse.Location as &quot;Location&quot;
        ,OnsiteCourse.Days as &quot;Days&quot;
        ,OnsiteCourse.Time as &quot;Time&quot;
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
</pre></div>

  [Création de la base de données School sur un serveur local]: #schooldb
  [Déploiement dans la base de données SQL]: #deploydb
  [Vérification du déploiement de la base de données]: #verify
  [Prise en main de l'administration de la base de données SQL]: /fr-fr/manage/services/sql-databases/getting-started-w-sql-databases/
