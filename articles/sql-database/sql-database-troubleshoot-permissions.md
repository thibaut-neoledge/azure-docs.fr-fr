<properties
	pageTitle="Comment effectuer des tâches d’administration, par exemple réinitialiser des mots de passe administrateur | Microsoft Azure"
	description="Explique comment effectuer des tâches d’administration courantes dans SQL Database. Par exemple, la réinitialisation de mot de passe administrateur, l’octroi et la suppression de l’accès."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""
	keywords="réinitialiser un mot de passe administrateur"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="v-shysun"/>

# Comment effectuer des tâches d’administration courantes telles que la réinitialisation de mot de passe d’administrateur dans Azure SQL Database
Utilisez cette rubrique pour accorder et refuser l’accès à une base de données SQL Azure en quelques opérations rapides. Pour obtenir des informations complètes, consultez :

- [Gestion des bases de données et des connexions dans Azure SQL Database](sql-database-manage-logins.md)
- [Sécurisation de votre base de données SQL](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Pour réinitialiser le mot de passe administrateur pour un serveur logique

- Dans le [portail Azure](https://portal.azure.com),cliquez sur **Serveurs SQL**, sélectionnez le serveur dans la liste, puis cliquez sur **Réinitialiser le mot de passe**.

## S’assurer que seules les adresses IP autorisées peuvent accéder au serveur
- Voir [Configuration des paramètres de pare-feu sur une base de données SQL](sql-database-configure-firewall-settings.md).

## Créer des utilisateurs de base de données à relation contenant-contenu dans la base de données utilisateur
- Utilisez l’instruction [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) et consultez [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).

## Pour authentifier les utilisateurs de base de données à relation contenant-contenu de la base de données à l’aide d’Azure Active Directory
- Consultez [Connexion à SQL Database avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).

## Pour créer des connexions supplémentaires pour les utilisateurs avec des privilèges élevés vers la base de données master virtuelle
- Utilisez l’instruction [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) et consultez la section Gestion des connexions de [Gestion des bases de données et des connexions dans Azure SQL Database](sql-database-manage-logins.md) pour plus de détails.

<!---HONumber=AcomDC_0914_2016-->