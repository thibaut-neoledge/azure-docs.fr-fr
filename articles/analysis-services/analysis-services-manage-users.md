---
title: "Gérer les utilisateurs dans Azure Analysis Services | Microsoft Docs"
description: "Découvrez comment gérer les utilisateurs sur un serveur Analysis Services dans Azure."
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
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 194910a3e4cb655b39a64d2540994d90d34a68e4
ms.openlocfilehash: 039ed6f4be9f3e0f6b92e5a9f11e12392912df9d
ms.lasthandoff: 02/16/2017


---
# <a name="manage-users-in-azure-analysis-services"></a>Gérer les utilisateurs dans Azure Analysis Services
Dans Azure Analysis Services, il existe deux types d’utilisateur : les administrateurs de serveur et les utilisateurs de base de données. 

## <a name="server-administrators"></a>Administrateurs de serveur
Vous pouvez utiliser les **Administrateurs Analysis Services** dans le panneau de commande de votre serveur dans le portail ou Propriétés du serveur dans SSMS pour gérer les administrateurs de serveur. Les administrateurs Analysis Services sont des administrateurs de serveur de base de données disposant de droits pour les tâches d’administration de base de données courantes comme l’ajout et la suppression de bases de données et la gestion des utilisateurs. Par défaut, l’utilisateur qui crée le serveur dans le portail Azure est automatiquement ajouté en tant qu’administrateur Analysis Services.

![Administrateurs de serveur dans le portail Azure](./media/analysis-services-manage-users/aas-manage-users-admins.png)

Vous devez également connaître les informations suivantes :

* Windows Live ID n’est pas un type d’identité pris en charge pour Azure Analysis Services.  
* Les administrateurs Analysis Services doivent être des utilisateurs Azure Active Directory valides.
* Lors de la création d’un serveur Azure Analysis Services via des modèles Azure Resource Manager, les administrateurs Analysis Services utilisent un tableau JSON des utilisateurs qui doivent être ajoutés en tant qu’administrateurs.

Les administrateurs Analysis Services peut être différents des administrateurs de ressources Azure, qui peuvent gérer les ressources pour les abonnements Azure. Cela maintient la compatibilité avec les comportements de gestion XMLA et TSML existants dans Analysis Services et vous permet de répartir les responsabilités entre la gestion des ressources Azure et la gestion de bases de données Analysis Services. Pour afficher tous les rôles et types d’accès de votre ressource Azure Analysis Services, utilisez le contrôle d’accès (IAM) dans le panneau de commande.

### <a name="to-add-administrators-using-azure-portal"></a>Pour ajouter des administrateurs à l’aide du portail Azure
1. Dans le panneau de contrôle de votre serveur, cliquez sur **Administrateurs Analysis Services**.
2. Dans le panneau **\<nom_serveur > Administrateurs Analysis Services**, cliquez sur **Ajouter**.
3. Dans le panneau **Ajouter des administrateurs de serveur**, sélectionnez les comptes d’utilisateur à ajouter.

## <a name="database-users"></a>Utilisateurs de base de données
Les utilisateurs de base de données doivent être ajoutés aux rôles de base de données. Les rôles définissent les utilisateurs et les groupes qui ont les mêmes autorisations sur une base de données. Par défaut, les bases de données tabulaires ont un rôle d’utilisateur par défaut avec des autorisations de lecture. Pour en savoir plus, consultez [Rôles dans les modèles tabulaires](https://msdn.microsoft.com/library/hh213165.aspx).

Les utilisateurs de la base de données de modèle Azure Analysis Services *doivent se trouver dans votre Azure Active Directory*. Les noms d’utilisateur doivent être spécifiés par adresse de messagerie professionnelle ou par UPN. Cela diffère des bases de données tabulaires locales qui prennent en charge les utilisateurs par les noms d’utilisateur de domaine Windows. 

Vous pouvez créer des rôles de base de données, ajouter des utilisateurs et des groupes aux rôles et configurer la sécurité de niveau ligne dans SQL Server Data Tools (SSDT) ou dans SQL Server Management Studio (SSMS). Vous pouvez également ajouter des utilisateurs aux rôles (ou les en supprimer) à l’aide des [applets de commande PowerShell Analysis Services](https://msdn.microsoft.com/library/hh758425.aspx) ou du langage [Tabular Model Scripting Language](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL).

**Exemple de script TMSL**

Dans cet exemple, un utilisateur et un groupe sont ajoutés au rôle Utilisateurs pour la base de données SalesBI.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle

Les administrateurs d’abonnement peuvent utiliser l’option **Contrôle d’accès** du panneau de contrôle pour configurer les rôles. Cette procédure est différente de celle appliquée aux administrateurs de serveur ou aux utilisateurs de base de données, dont la configuration s’effectue au niveau du serveur ou de la base de données (comme indiqué ci-dessus). 

![Contrôle des accès dans le portail Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Les rôles s’appliquent aux utilisateurs ou comptes qui doivent effectuer des tâches dans le portail ou à l’aide de modèles Azure Resource Manager. Pour en savoir plus, consultez [Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-what-is.md).

## <a name="next-steps"></a>Étapes suivantes
Si vous n’avez pas déjà déployé un modèle tabulaire sur votre serveur, c’est le moment de le faire. Pour en savoir plus, voir [Déployer sur Azure Analysis Services](analysis-services-deploy.md).

Si vous avez déployé un modèle sur votre serveur, vous êtes prêt à vous connecter à celui-ci à l’aide d’un client ou d’un navigateur. Pour en savoir plus, voir [Obtenir les données du serveur Azure Analysis Services](analysis-services-connect.md).


