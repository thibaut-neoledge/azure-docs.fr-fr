---
title: "Effectuer des tâches d’administration, par exemple réinitialiser des mots de passe administrateur | Microsoft Docs"
description: "Explique comment effectuer des tâches d’administration courantes dans SQL Database. Par exemple, la réinitialisation de mot de passe administrateur, l’octroi et la suppression de l’accès."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "réinitialiser un mot de passe administrateur"
ms.assetid: 9803fdcf-6501-4ac4-8cd0-f80071e052e1
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1a175572bf541f0be3936ad8934f52ea6b90dfa1


---
# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Comment effectuer des tâches d’administration courantes telles que la réinitialisation de mot de passe d’administrateur dans Azure SQL Database
Utilisez cette rubrique pour accorder et refuser l’accès à une base de données SQL Azure en quelques opérations rapides. Pour obtenir des informations complètes, consultez :

* [Gestion des bases de données et des connexions dans Azure SQL Database](sql-database-manage-logins.md)
* [Sécurisation de votre base de données SQL](sql-database-security.md)
* [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>Pour réinitialiser le mot de passe administrateur pour un serveur logique
* Dans le [portail Azure](https://portal.azure.com), cliquez sur **Serveurs SQL**, sélectionnez le serveur dans la liste, puis cliquez sur **Réinitialiser le mot de passe**.

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>S’assurer que seules les adresses IP autorisées peuvent accéder au serveur
* Voir [Configuration des paramètres de pare-feu sur une base de données SQL](sql-database-configure-firewall-settings.md).

## <a name="to-create-contained-database-users-in-the-user-database"></a>Créer des utilisateurs de base de données à relation contenant-contenu dans la base de données utilisateur
* Utilisez l’instruction [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) et consultez [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Pour authentifier les utilisateurs de base de données à relation contenant-contenu de la base de données à l’aide d’Azure Active Directory
* Consultez [Connexion à SQL Database avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>Pour créer des connexions supplémentaires pour les utilisateurs avec des privilèges élevés vers la base de données master virtuelle
* Utilisez l’instruction [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) et consultez la section Gestion des connexions de [Gestion des bases de données et des connexions dans Azure SQL Database](sql-database-manage-logins.md) pour plus de détails.




<!--HONumber=Nov16_HO3-->


