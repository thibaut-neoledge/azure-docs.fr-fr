---
title: "Configurer Multi-Factor Authentication - Azure SQL | Microsoft Docs"
description: "Utilisez l’authentification multi-facteur avec SSMS pour la base de données SQL et SQL Data Warehouse."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/08/2017
ms.author: rickbyh
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 1815ea909e35a02b82b4c836d7baaf6390d20bdd
ms.contentlocale: fr-fr
ms.lasthandoff: 06/09/2017


---
# <a name="configure-azure-sql-database-multi-factor-authentication-for-sql-server-management-studio"></a>Configuration de l’authentification multifacteur aux bases de données Azure SQL pour SQL Server Management Studio

Cette rubrique montre comment configurer l’authentification multifacteur aux bases de données Azure SQL pour SQL Server Management Studio. 

Pour une vue d’ensemble de l’authentification multifacteur Azure SQL Database, consultez [Authentification universelle avec SQL Database et SQL Data Warehouse (prise en charge de SSMS pour MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Configuration

1. **Configurer un annuaire Azure Active Directory** : pour plus d’informations, consultez [Intégration de vos identités locales avec Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Ajout de votre nom de domaine personnalisé à Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure prend désormais en charge la fédération avec Windows Server Active Directory), [Administration de votre annuaire Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) et [Gestion d’Azure AD à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurez l’authentification MFA** : pour obtenir des instructions pas à pas, consultez [Accès conditionnel (MFA) avec Azure SQL Database et Data Warehouse](sql-database-conditional-access.md). 
3. **Configurer SQL Database ou SQL Data Warehouse pour l’authentification Azure AD** : pour obtenir des instructions pas à pas, consultez [Connexion à SQL Database ou SQL Data Warehouse avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).
4. **Télécharger SSMS** : sur l’ordinateur client, téléchargez la dernière version de SSMS (au minimum celle d’août 2016) à partir de la page [Télécharger SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Connexion à l’aide de l’authentification universelle avec SSMS

Les étapes suivantes montrent comment se connecter à la base de données SQL ou à SQL Data Warehouse à l’aide de la version la plus récente de SSMS.

1. Pour vous connecter à l’aide de l’authentification universelle, accédez à la boîte de dialogue **Se connecter au serveur**, puis sélectionnez **Authentification universelle Active Directory**.

   ![1mfa-universal-connect][1]
2. Comme d’habitude pour SQL Database et SQL Data Warehouse, vous devez cliquer sur **Options** et spécifier la base de données dans la boîte de dialogue **Options**. Puis, cliquez sur **Se connecter**.
3. Lorsque la boîte de dialogue **Connectez-vous à votre compte** s’affiche, spécifiez le compte et le mot de passe de votre identité Azure Active Directory.

   ![2mfa-sign-in][2]
   
   > [!NOTE]
   > Pour l’authentification universelle avec un compte qui ne nécessite pas MFA, vous vous connectez à ce stade. Pour les utilisateurs nécessitant MFA, passez aux étapes suivantes :
   > 
   > 
4. Deux boîtes de dialogue de configuration de MFA peuvent s’afficher. Cette opération à exécuter une seule fois dépend de l’administrateur du paramètre de l’administrateur MFA et peut être par conséquent facultative. Pour un domaine avec MFA activée, cette étape est parfois prédéfinie (par exemple, le domaine exige que les utilisateurs utilisent une carte à puce et un code confidentiel).  

   ![3mfa-setup][3]
5. La deuxième boîte de dialogue unique possible vous permet de sélectionner les détails de votre méthode d’authentification. Les options possibles sont configurées par votre administrateur.

   ![4mfa-verify-1][4]
6. Azure Active Directory vous envoie les informations de confirmation. Lorsque vous recevez le code de vérification, entrez-le dans la zone **Entrez le code de vérification**, puis cliquez sur **Connexion**.

   ![5mfa-verify-2][5]

Lorsque la vérification est terminée, SSMS se connecte normalement à condition que les informations d’identification soient valides et qu’un accès au pare-feu soit possible.

## <a name="next-steps"></a>Étapes suivantes

* Pour une vue d’ensemble de l’authentification multifacteur Azure SQL Database, consultez Authentification universelle avec SQL Database et SQL Data Warehouse (prise en charge de SSMS pour MFA).
* Octroyer à d’autres utilisateurs l’accès à votre base de données : [Authentification et autorisation de base de données SQL : octroi de l’accès](sql-database-manage-logins.md)  
Vérifiez que les autres utilisateurs peuvent se connecter par le biais du pare-feu : [Configurer une règle de pare-feu au niveau du serveur sur Azure SQL Database à l’aide du portail Azure](sql-database-configure-firewall-settings.md)

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png


