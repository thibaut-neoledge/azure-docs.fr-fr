---
title: "Multi-Factor Authentication - Azure SQL | Microsoft Docs"
description: "Utilisez l’authentification multi-facteur avec SSMS pour la base de données SQL et SQL Data Warehouse."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 6e68ff56eaab3de8166711c872bf1b5707dbdf56
ms.openlocfilehash: ce77a059a05f83d74e80c71a87a22de6755b2dbb
ms.lasthandoff: 02/16/2017


---
# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Prise en charge de SSMS pour Azure AD MFA avec la base de données SQL et SQL Data Warehouse
La base de données SQL Azure et Azure SQL Data Warehouse prennent désormais en charge les connexions à partir de SQL Server Management Studio (SSMS) à l’aide de *l’authentification universelle Active Directory*. L’authentification universelle Active Directory est un workflow interactif prenant en charge *Azure Multi-Factor Authentication* (MFA). Azure MFA contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Il assure une authentification forte avec une gamme d'options de vérification simples (appel téléphonique, SMS, cartes à puce avec code PIN ou notification d'application mobile) pour permettre aux utilisateurs de choisir leur méthode préférée. 

* Pour une description de Multi-Factor Authentication, consultez la rubrique [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
* Pour les étapes de configuration, consultez [Configuration de l’authentification multifacteur aux bases de données Azure SQL pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

## <a name="multi-factor-options"></a>Options de multifacteur

SSMS prend désormais en charge :

* l’authentification multifacteur (MFA) interactive avec Azure AD avec l’affichage possible d’une boîte de dialogue contextuelle de validation ;
* un mot de passe Active Directory non-interactif et les méthodes d’authentification Active Directory intégrées qui peuvent être utilisés dans de nombreuses applications (ADO.NET, JDBC, ODBC, etc.). Ces deux méthodes n’entraînent jamais l’affichage de boîtes de dialogue contextuelles.

Lorsque le compte d’utilisateur est configuré pour MFA, le workflow d’authentification interactif nécessite une interaction supplémentaire de l’utilisateur (par exemple dans des boîtes de dialogue contextuelles ou avec l’utilisation d’une carte à puce, etc.). Lorsque le compte d’utilisateur est configuré pour MFA, l’utilisateur doit sélectionner l’authentification universelle Azure pour se connecter. Si le compte d’utilisateur ne nécessite pas MFA, l’utilisateur peut toujours utiliser les deux autres options d’authentification Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limites de l’authentification universelle pour la base de données SQL et SQL Data Warehouse
* SSMS est le seul outil actuellement activé pour MFA par le biais de l’authentification universelle Active Directory.
* Seul un compte Azure Active Directory unique peut se connecter à une instance de SSMS à l’aide de l’authentification universelle. Pour vous connecter comme un autre compte Azure AD, vous devez utiliser une autre instance de SSMS. (Cette restriction est limitée à l’authentification universelle Active Directory. Vous pouvez vous connecter à différents serveurs à l’aide de l’authentification de mot de passe Active Directory, l’authentification intégrée à Active Directory ou l’authentification SQL Server).
* SSMS prend en charge l’authentification universelle Active Directory pour la visualisation de l’Explorateur d’objets, de l’Éditeur de requête et du magasin de requêtes.
* Ni DacFx, ni le Concepteur de schémas ne prennent en charge l’authentification universelle.
* Les comptes MSA ne sont pas pris en charge pour l’authentification universelle Active Directory.
* L’authentification universelle Active Directory n’est pas prise en charge dans SSMS pour les utilisateurs qui sont importés dans l’Active Directory actuel à partir d’autres annuaires Azure Active Directory. Ces utilisateurs ne sont pas pris en charge, car cela nécessiterait un ID de locataire pour valider les comptes et aucun mécanisme ne fournit cette possibilité.
* Il n’existe aucune configuration logicielle supplémentaire nécessaire pour l’authentification universelle Active Directory, sauf que vous devez utiliser une version prise en charge de SSMS.



## <a name="next-steps"></a>Étapes suivantes

* Pour les étapes de configuration, consultez [Configuration de l’authentification multifacteur aux bases de données Azure SQL pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
* Octroyer à d’autres utilisateurs l’accès à votre base de données : [Authentification et autorisation de base de données SQL : octroi de l’accès](sql-database-manage-logins.md)  
Vérifiez que les autres utilisateurs peuvent se connecter par le biais du pare-feu : [Configurer une règle de pare-feu au niveau du serveur sur Azure SQL Database à l’aide du portail Azure](sql-database-configure-firewall-settings.md)



