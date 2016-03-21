<properties
   pageTitle="Azure AD Connect Sync : connecteur SQL générique - Guide pas à pas | Microsoft Azure"
   description="Cet article vous guide dans une procédure pas à pas pour créer un simple système de base de données Ressources humaines à l’aide du connecteur SQL générique."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/08/2016"
   ms.author="andkjell"/>

# Connecteur SQL générique - Guide pas à pas
Cette rubrique est un guide pas à pas. Elle explique comment créer un simple exemple de base de données Ressources humaines et l’utiliser pour importer certains utilisateurs et leur appartenance à un groupe.

## Préparer l’exemple de base de données
Sur un serveur exécutant SQL Server, exécutez le script SQL disponible dans l’[Annexe A](#appendix-a). Un exemple de base de données portant le nom GSQLDEMO est créé. Le modèle objet pour la base de données créée a l’aspect suivant :

![Modèle objet](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

Créez également l’utilisateur que vous souhaitez utiliser pour vous connecter à la base de données. Dans cette procédure pas à pas, l’utilisateur est appelé FABRIKAM\\SQLUser et il est situé dans le domaine.

## Créer le fichier de connexion ODBC
Le connecteur SQL générique utilise ODBC pour se connecter au serveur distant. Nous devons tout d’abord créer un fichier avec les informations de connexion ODBC.

1. Démarrez l’utilitaire de gestion ODBC sur votre serveur : ![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. Sélectionnez l’onglet **Fichier DSN**. Cliquez sur **Ajouter...**. ![ODBC1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. Le pilote prêt à l’emploi fonctionne parfaitement. Par conséquent, sélectionnez-le, puis cliquez sur **Suivant>**. ![ODBC2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. Nommez le fichier, par exemple **GenericSQL**. ![ODBC3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. Cliquez sur **Terminer**. ![ODBC4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. Il est temps de configurer la connexion. Décrivez la source de données et fournissez le nom du serveur exécutant SQL Server. ![ODBC5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. Sélectionnez le mode d’authentification avec SQL. Dans ce cas, nous allons utiliser l’authentification Windows. ![ODBC6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. Indiquez le nom de l’exemple de base de données **GSQLDEMO**. ![ODBC7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. Conservez toutes les valeurs par défaut sur cet écran. Cliquez sur **Terminer**. ![ODBC8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. Pour vérifier que tout fonctionne comme prévu, cliquez sur **Tester la source de données**. ![ODBC9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. Vérifiez que le test a réussi. ![ODBC10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. Le fichier de configuration ODBC doit maintenant être visible dans le fichier DSN. ![ODBC11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

Nous avons maintenant le fichier dont nous avons besoin et pouvons commencer à créer le connecteur.

## Créer le connecteur SQL générique

1. Dans l’interface Synchronization Service Manager, cliquez sur **Connecteurs**, puis sur **Créer**. Sélectionnez **SQL générique (Microsoft)** et donnez-lui un nom descriptif. ![Connecteur1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. Recherchez le fichier DSN que vous avez créé dans la section précédente et téléchargez-le sur le serveur. Entrez les informations de connexion à la base de données. ![Connecteur2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. Dans cette procédure pas à pas, pour faciliter la procédure, disons qu’il existe deux types d’objet : **Utilisateur** et **Groupe**. ![Connecteur3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. Pour rechercher les attributs, nous voulons que le connecteur les détecte en examinant la table elle-même. Étant donné que **Utilisateurs** est un mot réservé dans SQL, nous devons l’indiquer entre crochets [ ]. ![Connecteur4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. Il est temps de définir l’attribut d’ancrage et l’attribut de nom de domaine. Pour **Utilisateurs**, nous allons utiliser la combinaison des deux attributs username et EmployeeID. Pour **Groupe**, nous allons utiliser GroupName (rappelons qu’il ne s’agit ici que d’un exemple). ![Connecteur5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. Tous les types d’attribut ne peuvent pas être détectés dans une base de données SQL, le type d’attribut de référence en particulier. Pour le type d’objet de groupe, nous devons modifier OwnerID et MemberID en attributs de référence. ![Connecteur6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. Les attributs que nous avons sélectionnés en tant qu’attributs de référence à l’étape précédente nécessitent maintenant le type d’objet auquel ils font référence. Dans notre cas, il s’agit du type d’objet User. ![Connecteur7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. Dans la page Paramètres généraux, sélectionnez **Filigrane** comme stratégie delta. Entrez également au format de date/heure **yyyy-MM-dd HH:mm:ss**. ![Connecteur8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. Dans la page **Configurer les partitions**, sélectionnez les deux types d’objet. ![Connecteur9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. Dans **Sélectionner les types d’objet** et **Sélectionner les attributs**, sélectionnez les types d’objet et tous les attributs. Sur la page **Configurer les ancres**, cliquez sur **Terminer**.

## Créer les profils d’exécution

1. Dans l’interface Synchronization Service Manager, cliquez sur **Connecteurs**, puis sur **Configurer les profils d’exécution**. Cliquez sur **Nouveau profil**. Nous allons commencer par **Importation intégrale**. ![ProfilExecution1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. Sélectionnez le type **Importation intégrale (intermédiaire uniquement)**. ![ProfilExecution2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. Sélectionnez la partition **OBJECT=User**. ![ProfilExecution3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. Sélectionnez **Table** et tapez **[USERS]**. Faites défiler jusqu’à la section de type d’objet à valeurs multiples et entrez les données tel qu’indiqué ci-dessous. Sélectionnez **Terminer** pour enregistrer l’étape. ![ProfilExecution4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png) ![ProfilExecution4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)  
5. Sélectionnez **Nouvelle étape**. Cette fois, sélectionnez **OBJECT=Group**. Sur la dernière page, utilisez la configuration ci-dessous. Cliquez sur **Terminer**. ![ProfilExecution5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png) ![ProfilExecution5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)  
6. (Facultatif) Vous pouvez configurer des profils d’exécution supplémentaires si vous le souhaitez. Pour cette procédure pas à pas, seule l’importation complète est utilisée.
7. Cliquez sur **OK** pour terminer de modifier les profils d’exécution.

## Ajouter quelques données de test et tester l’importation

Remplissez quelques données de test dans votre exemple de base de données. Lorsque vous êtes prêt, sélectionnez **Exécuter** et **Importation complète**.

Voici un utilisateur avec deux numéros de téléphone et un groupe avec quelques membres. ![cs1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png) ![cs2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)

## annexe A
**Script SQL pour créer l’exemple de base de données**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
	[MemberID] [int] NOT NULL,
	[Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
	[GroupID] [int] NOT NULL,
	[GROUPNAME] [nvarchar](200) NOT NULL,
	[DESCRIPTION] [nvarchar](200) NULL,
	[WATERMARK] [datetime] NULL,
	[OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
	[GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
	[USER_ID] [int] NULL,
	[Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
	[USERID] [int] NOT NULL,
	[USERNAME] [nvarchar](200) NOT NULL,
	[FirstName] [nvarchar](100) NULL,
	[LastName] [nvarchar](100) NULL,
	[DisplayName] [nvarchar](100) NULL,
	[ACCOUNTDISABLED] [bit] NULL,
	[EMPLOYEEID] [int] NOT NULL,
	[WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
	[USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```

<!---HONumber=AcomDC_0309_2016-->