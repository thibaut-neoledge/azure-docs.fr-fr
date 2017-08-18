---
title: "Gérer les administrateurs de serveur dans Azure Analysis Services | Microsoft Docs"
description: "Apprenez à gérer des administrateurs de serveur pour un serveur Analysis Services dans Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/22/2016
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 25c8b49401486edfad0a1a60fe2258a586f6adc2
ms.contentlocale: fr-fr
ms.lasthandoff: 06/23/2017


---
# <a name="manage-server-administrators"></a>Gérer des administrateurs de serveur
Un administrateur de serveur doit être un utilisateur ou un groupe valide présent dans Azure Active Directory (Azure AD) pour le client disposant du serveur. Vous pouvez utiliser les **Administrateurs Analysis Services** dans le panneau de commande de votre serveur dans le portail ou Propriétés du serveur dans SSMS pour gérer les administrateurs de serveur. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Pour ajouter des administrateurs de serveur à l’aide du portail Azure
1. Dans le panneau de contrôle de votre serveur, cliquez sur **Administrateurs Analysis Services**.
2. Dans le panneau **\<nom_serveur > Administrateurs Analysis Services**, cliquez sur **Ajouter**.
3. Dans le panneau **Ajouter des administrateurs de serveur**, sélectionnez les comptes d’utilisateur depuis Azure AD ou invitez des utilisateurs externes via leur adresse e-mail.

    ![Administrateurs de serveur dans le portail Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Pour ajouter des administrateurs de serveur à l’aide de SSMS
1. Faites un clic droit sur le serveur > **Propriétés**.
2. Dans **Propriétés de Analysis Server**, cliquez sur **Sécurité**.
3. Cliquez sur **Ajouter**, puis entrez l’adresse e-mail d’un utilisateur ou d’un groupe dans Azure AD.
   
    ![Ajouter des administrateurs de serveur dans SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Étapes suivantes 
[Authentification et autorisations utilisateur](analysis-services-manage-users.md)  
[Gérer les utilisateurs et rôles de base de données](analysis-services-database-users.md)  
[Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-what-is.md)  


