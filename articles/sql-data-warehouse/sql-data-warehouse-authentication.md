<properties
   pageTitle="Authentification sur Azure SQL Data Warehouse | Microsoft Azure"
   description="Authentification Azure Active Directory (AAD) et SQL Server sur Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/04/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# Authentification sur Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-connect-overview.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Pilotes](sql-data-warehouse-connection-strings.md)

Pour vous connecter à SQL Data Warehouse, vous devez transmettre des informations d’identification de sécurité à des fins d’authentification. Lors de l’établissement d’une connexion, vous découvrirez également que certains paramètres de connexion sont configurés dans le cadre de l’établissement de votre session de requête.

Vous pouvez consulter l’article [Sécuriser une base de données dans SQL Data Warehouse][] pour plus d’informations sur la sécurité et la manière d’activer des connexions à votre entrepôt de données.

## Authentification SQL
Pour vous connecter à SQL Data Warehouse, vous devrez fournir les informations suivantes :

- Nom complet du serveur
- Authentification SQL
- Nom d’utilisateur
- Mot de passe
- Base de données par défaut (facultatif)

Par défaut, votre connexion se connecte à la base de données MASTER et non à votre base de données utilisateur. Pour vous connecter à votre base de données utilisateur, vous pouvez choisir d’effectuer l’une des deux opérations suivantes :

1. Spécifiez la base de données par défaut lors de l’inscription de votre serveur auprès de l’Explorateur d’objets SQL Server dans SQL Server Data Tools (SSDT), SSMS, ou dans votre chaîne de connexion d’application. Par exemple, insérez le paramètre InitialCatalog pour une connexion ODBC.
2. Commencez par sélectionner la base de données utilisateur avant de créer une session dans SSDT.

> [AZURE.NOTE] Pour obtenir des conseils en matière de connexion à SQL Data Warehouse avec SSDT, consultez l’article [Envoyer des requêtes avec Visual Studio][].

Il est important de noter que l’instruction Transact-SQL **USE <votre base de données>** n’est pas prise en charge pour la modification de la base de données pour une connexion


## Authentification Azure Active Directory (AAD)

L’authentification [Azure Active Directory][What is Azure Active Directory] est un mécanisme servant à se connecter à Microsoft Azure SQL Data Warehouse à l’aide d’identités dans Azure Active Directory (Azure AD). Avec l’authentification Azure Active Directory, vous pouvez gérer de manière centralisée les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé. La gestion centralisée des ID fournit un emplacement unique pour gérer les utilisateurs SQL Data Warehouse et elle simplifie la gestion des autorisations.

### Avantages

Les avantages suivants sont inclus :

- Il fournit une alternative à l’authentification SQL Server.
- Permet de bloquer la prolifération des identités utilisateur sur plusieurs serveurs de base de données.
- Permet la rotation de mot de passe à un emplacement unique
- Les clients peuvent gérer les autorisations de base de données à l’aide de groupes (AAD) externes.
- Il peut éliminer le stockage des mots de passe en activant l’authentification intégrée Windows et les autres formes d’authentification prises en charge par Azure Active Directory.
- L’authentification Azure Active Directory utilise les utilisateurs de base de données à relation contenant-contenu pour authentifier les identités au niveau de la base de données.
- Azure Active Directory prend en charge l’authentification basée sur les jetons pour les applications se connectant à SQL Data Warehouse.


### Configuration

Les étapes de configuration incluent les procédures suivantes pour configurer et utiliser l’authentification Azure Active Directory.

1. Créer et renseigner un répertoire Azure Active Directory
2. Facultatif : associer ou modifier le répertoire actif actuellement associé à votre abonnement Azure
3. Créer un administrateur Azure Active Directory pour Azure SQL Data Warehouse
4. Configurer vos ordinateurs clients
5. Créer des utilisateurs de base de données à relation contenant-contenu dans votre base de données mappés sur les identités Azure AD
6. Se connecter à l’entrepôt de données à l’aide des identités Azure AD

Actuellement, les utilisateurs Azure Active Directory ne sont pas affichés dans l’Explorateur d’objets SSDT. Pour résoudre ce problème, affichez les utilisateurs dans [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
### Rechercher des informations détaillées
- Effectuez les étapes détaillées. Les étapes détaillées de configuration et d’utilisation de l’authentification Azure Active Directory sont presque identiques pour Base de données SQL Azure et Azure SQL Data Warehouse. Suivez les étapes détaillées dans la rubrique [Connexion au service Base de données SQL ou SQL Data Warehouse avec l’authentification Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
- Créez des rôles de base de données personnalisés et ajoutez des utilisateurs aux rôles. Accordez ensuite des autorisations granulaires aux rôles. Pour plus d’informations, consultez [Prise en main des autorisations du moteur de base de données](https://msdn.microsoft.com/library/mt667986.aspx).

## Étapes suivantes

Pour commencer à interroger votre entrepôt de données avec Visual Studio et d’autres applications, consultez [Soumettre des requêtes avec Visual Studio][].

<!-- Article references -->
[Sécuriser une base de données dans SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Envoyer des requêtes avec Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Soumettre des requêtes avec Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md

<!---HONumber=AcomDC_0810_2016-->