<properties
	pageTitle="Dépannage des autorisations et de l’accès à la base de données SQL Azure"
	description="Opérations rapides à suivre pour résoudre les problèmes communs relatifs aux autorisations, à l’accès, aux utilisateurs et aux connexions"
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="v-shysun"/>

#Résoudre les problèmes relatifs aux autorisations et à l’accès aux bases de données Azure SQL
Utilisez cette rubrique pour accorder et refuser l’accès à une base de données SQL Azure en quelques opérations rapides. Pour obtenir des informations complètes, consultez :

- [Gestion des bases de données et des connexions dans Azure SQL Database](sql-database-manage-logins.md)
- [Sécurisation de votre base de données SQL](sql-database-security)
- [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

##Modifier le mot de passe d’administration d’un serveur logique
- Dans le [portail Azure](https://portal.azure.com),cliquez sur **Serveurs SQL**, sélectionnez le serveur dans la liste, puis cliquez sur **Réinitialiser le mot de passe**.
##S’assurer que seules les adresses IP autorisées peuvent accéder au serveur
- Voir [Configuration des paramètres de pare-feu sur une base de données SQL](sql-database-configure-firewall-settings.md).

##Créer des utilisateurs de base de données à relation contenant-contenu dans la base de données utilisateur
- Utilisez l’instruction [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) et consultez la base de données à relation contenant-contenu [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).

## Pour authentifier les utilisateurs de base de données à relation contenant-contenu de la base de données à l’aide d’Azure Active Directory
- Voir connexion à [SQL Database en utilisant en utilisant l’authentification Azure Active Directory](sql-database-aad-authentication.md)

## Pour créer des connexions supplémentaires pour les utilisateurs avec des privilèges élevés vers la base de données master virtuelle
- Utilisez l’instruction [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx), et consultez la section Gestion des connexions de [Gestion des bases de données et des connexions dans la base de données SQL Azure](sql-database-manage-logins.md) pour plus de détails.

<!---HONumber=AcomDC_1217_2015-->