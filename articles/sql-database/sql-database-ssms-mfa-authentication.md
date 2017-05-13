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
ms.custom: security-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 04/07/2017
ms.author: rickbyh
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 33282f1ce0c31056524359aeb42399be550334e2
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Authentification universelle avec SQL Database et SQL Data Warehouse (prise en charge de SSMS pour MFA)
Azure SQL Database et Azure SQL Data Warehouse prennent en charge les connexions à partir de SQL Server Management Studio (SSMS) à l’aide de *l’authentification universelle Active Directory*. 

- Authentification universelle Active Directory prend en charge les deux méthodes d’authentification non interactives (authentification par mot de passe Active Directory et authentification intégrée Active Directory). Un mot de passe Active Directory non-interactif et les méthodes d’authentification Active Directory intégrées qui peuvent être utilisés dans de nombreuses applications (ADO.NET, JDBC, ODBC, etc.). Ces deux méthodes n’entraînent jamais l’affichage de boîtes de dialogue contextuelles.

- L’authentification universelle Active Directory est une méthode interactive prenant également en charge *Azure Multi-Factor Authentication* (MFA). Azure MFA contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Il assure une authentification forte avec une gamme d'options de vérification simples (appel téléphonique, SMS, cartes à puce avec code PIN ou notification d'application mobile) pour permettre aux utilisateurs de choisir leur méthode préférée. L’authentification multifacteur (MFA) interactive avec Azure AD peut afficher une boîte de dialogue contextuelle de validation.

Pour une description de Multi-Factor Authentication, consultez la rubrique [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
Pour les étapes de configuration, consultez [Configuration de l’authentification multifacteur aux bases de données Azure SQL pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nom de domaine Azure AD et paramètre d’ID de locataire   

À partir de [la version 17 de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), les utilisateurs qui sont importés dans le répertoire Active Directory en cours à partir d’autres répertoires Azure Active Directory peuvent fournir le nom de domaine Azure AD ou l’ID de locataire lorsqu’ils se connectent. L’**authentification universelle Active Directory** est ainsi autorisée pour identifier l’autorité d’authentification appropriée. Cette option est également requise pour prendre en charge les comptes Microsoft (MSA) tels que outlook.com, hotmail.com ou live.com. Les utilisateurs qui souhaitent être authentifiés à l’aide de l’authentification universelle doivent entrer leur nom de domaine Azure AD ou leur ID de locataire. Ce paramètre représente le nom de domaine/ID de locataire Azure AD lié au serveur Azure. Par exemple, si le serveur Azure est associé au domaine Azure AD `contosotest.onmicrosoft.com` où l’utilisateur `joe@contosodev.onmicrosoft.com` est hébergé en tant qu’utilisateur importé du domaine Azure AD `contosodev.onmicrosoft.com`, le nom de domaine requis pour authentifier cet utilisateur est `contosotest.onmicrosoft.com`. Lorsque l’utilisateur est un utilisateur natif d’Azure AD lié au serveur Azure et qu’il n’a pas de compte MSA, aucun nom de domaine ou ID de locataire n’est requis. Pour entrer le paramètre (à partir de SSMS version 17), complétez la boîte de dialogue **Se connecter à la base de données** en sélectionnant **Authentification universelle Active Directory**, cliquez sur **Options**, sur l’onglet **Propriétés de connexion**, vérifiez la zone **Nom du domaine AD ou ID de locataire** et indiquez l’autorité d’authentification, telle que le nom de domaine (**contosotest.onmicrosoft.com**) ou le GUID de l’ID de locataire.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limites de l’authentification universelle pour la base de données SQL et SQL Data Warehouse
* SSMS est le seul outil actuellement activé pour MFA par le biais de l’authentification universelle Active Directory.
* Seul un compte Azure Active Directory unique peut se connecter à une instance de SSMS à l’aide de l’authentification universelle. Pour vous connecter comme un autre compte Azure AD, vous devez utiliser une autre instance de SSMS. (Cette restriction est limitée à l’authentification universelle Active Directory. Vous pouvez vous connecter à différents serveurs à l’aide de l’authentification de mot de passe Active Directory, l’authentification intégrée à Active Directory ou l’authentification SQL Server).
* SSMS prend en charge l’authentification universelle Active Directory pour la visualisation de l’Explorateur d’objets, de l’Éditeur de requête et du magasin de requêtes.
* Ni DacFx, ni le Concepteur de schémas ne prennent en charge l’authentification universelle.
* Il n’existe aucune configuration logicielle supplémentaire nécessaire pour l’authentification universelle Active Directory, sauf que vous devez utiliser une version prise en charge de SSMS.


## <a name="next-steps"></a>Étapes suivantes

* Pour les étapes de configuration, consultez [Configuration de l’authentification multifacteur aux bases de données Azure SQL pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
* Octroyer à d’autres utilisateurs l’accès à votre base de données : [Authentification et autorisation de base de données SQL : octroi de l’accès](sql-database-manage-logins.md)  
Vérifiez que les autres utilisateurs peuvent se connecter par le biais du pare-feu : [Configurer une règle de pare-feu au niveau du serveur sur Azure SQL Database à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
* Pour plus d’informations sur la configuration et la gestion d’Azure AD, consultez [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](sql-database-aad-authentication-configure.md).



