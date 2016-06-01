<properties
   pageTitle="Connexion à SQL Data Warehouse avec l’authentification Azure Active Directory | Microsoft Azure"
   description="Apprenez à vous connecter à SQL Data Warehouse avec l’authentification Azure Active Directory."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="05/11/2016"
   ms.author="rick.byham@microsoft.com"/>

# Connexion à SQL Data Warehouse avec l’authentification Azure Active Directory


L’authentification Azure Active Directory est un mécanisme servant à se connecter à Microsoft Azure SQL Data Warehouse à l’aide d’identités dans Azure Active Directory (Azure AD). Avec l’authentification Azure Active Directory, vous pouvez gérer de manière centralisée les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé. La gestion centralisée des ID fournit un emplacement unique pour gérer les utilisateurs SQL Data Warehouse et elle simplifie la gestion des autorisations. Les avantages suivants sont inclus :

- Il fournit une alternative à l’authentification SQL Server.
- Permet de bloquer la prolifération des identités utilisateur sur plusieurs serveurs de base de données.
- Permet la rotation de mot de passe à un emplacement unique
- Les clients peuvent gérer les autorisations de base de données à l’aide de groupes (AAD) externes.
- Il peut éliminer le stockage des mots de passe en activant l’authentification intégrée Windows et les autres formes d’authentification prises en charge par Azure Active Directory.
- L’authentification Azure Active Directory utilise les utilisateurs de base de données à relation contenant-contenu pour authentifier les identités au niveau de la base de données.
- Azure Active Directory prend en charge l’authentification basée sur les jetons pour les applications se connectant à SQL Data Warehouse.

> [AZURE.IMPORTANT] L’authentification Azure Active Directory est une fonctionnalité préliminaire et est soumise aux conditions du contrat de licence (par exemple, l’accord Entreprise, l’accord Microsoft Azure ou le contrat d’abonnement Microsoft Online), ainsi qu’à toutes les [Conditions d’utilisation supplémentaires de la version préliminaire de Microsoft Azure applicable](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les étapes de configuration incluent les procédures suivantes pour configurer et utiliser l’authentification Azure Active Directory.

1. Créer et renseigner un répertoire Azure Active Directory
2. Facultatif : associer ou modifier le répertoire actif actuellement associé à votre abonnement Azure
3. Créer un administrateur Azure Active Directory pour Azure SQL Data Warehouse
4. Configurer vos ordinateurs clients
5. Créer des utilisateurs de base de données à relation contenant-contenu dans votre base de données mappés sur les identités Azure AD
6. Se connecter à l’entrepôt de données à l’aide des identités Azure AD

Les étapes détaillées de configuration et d’utilisation de l’authentification Azure Active Directory sont presque identiques pour Base de données SQL Azure et Azure SQL Data Warehouse. Suivez les étapes décrites dans la rubrique [Connexion au service Base de données SQL ou SQL Data Warehouse avec l’authentification Azure Active Directory](../sql-database/sql-database-aad-authentication.md).

Les principales différences entre l’utilisation de l’authentification Azure Active Directory avec Base de données SQL Azure et Azure SQL Data Warehouse résident dans le fait que vous devez utiliser SQL Server Data Tools au lieu de SQL Server Management Studio pour vous connecter à SQL Data Warehouse. SQL Data Warehouse requiert la version d’avril 2016 (14.0.60311.1) ou une version ultérieure de SQL Server Data Tools pour Visual Studio 2015. Actuellement, les utilisateurs Azure Active Directory ne sont pas affichés dans l’Explorateur d’objets SSDT. Pour résoudre ce problème, affichez les utilisateurs dans [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).

<!---HONumber=AcomDC_0518_2016-->