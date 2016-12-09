---
title: "Ajout ou modification de rôles d’administrateur Azure | Microsoft Docs"
description: "Décrit comment ajouter ou modifier un coadministrateur, administrateur de services et administrateur de compte Azure"
services: 
documentationcenter: 
author: genlin
manager: mbaldwin
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: abc3d4119cfde69ed24f7390dc99e65614a7f489


---
# <a name="how-to-add-or-change-azure-administrator-roles"></a>Ajout ou modification de rôles d’administrateur Azure
Il existe trois genres de rôles d’administrateur dans Microsoft Azure :

| Rôle administratif | Limite | Description |
| --- | --- | --- |
| Administrateur de compte |1 par compte Azure |Il s’agit de la personne qui a souscrit ou acheté des abonnements Azure et qui est autorisée à accéder au [Centre des comptes](https://account.windowsazure.com/Home/Index) et à effectuer diverses tâches de gestion. Ces tâches incluent la possibilité de créer et d’annuler des abonnements, de modifier la facturation d’un abonnement et de modifier l’administrateur de services fédérés. |
| Administrateur de services fédérés |1 par abonnement Azure |Ce rôle est autorisé à gérer les services sur le [portail Azure](https://portal.azure.com). Par défaut, pour un nouvel abonnement, l’administrateur de compte est également l’administrateur de services fédérés. |
| Coadministrateur (CA) dans le [portail Azure Classic](https://manage.windowsazure.com) |200 par abonnement |Ce rôle possède les mêmes privilèges d’accès que l’administrateur de services fédérés, à ceci près qu’il ne peut pas modifier la manière dont les abonnements sont associés aux répertoires Azure. |

> [!NOTE]
> Le contrôle d’accès en fonction du rôle (RBAC) Azure Active Directory permet d’ajouter les utilisateurs à plusieurs rôles. Pour plus d’informations, consultez la rubrique [Contrôle d’accès en fonction du rôle Azure Active Directory](active-directory/role-based-access-control-configure.md).
> 
> [!NOTE]
> Si à un moment donné dans cet article, vous rencontrez un problème et avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide.
> 
> 

## <a name="how-to-add-an-admin-for-a-subscription"></a>Comment ajouter un administrateur à un abonnement
**Portail Azure**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Abonnement** > *l’abonnement auquel l’administrateur doit accéder*.
   
    ![newselectsub](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)
3. Dans le panneau de l’abonnement, sélectionnez **Paramètres**> **Utilisateurs**.
   
    ![newsettings](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. Dans le panneau Utilisateurs, sélectionnez **Ajouter**>**Sélectionner un rôle** > **Propriétaire**.
   
    ![newselectrole](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)
   
    **Remarque :**
   
   * Le rôle de propriétaire possède les mêmes privilèges d’accès que le coadministrateur. Ce rôle n’a pas de privilèges d’accès au [Centre des comptes Azure](https://account.windowsazure.com/subscriptions).
   * Les propriétaires ajoutés via le [portail Azure](https://portal.azure.com) ne peuvent pas gérer les services dans le [portail Azure Classic](https://manage.windowsazure.com).  
5. Entrez l’adresse de messagerie de l’utilisateur que vous souhaitez ajouter en tant que propriétaire, cliquez sur l’utilisateur, puis sur **Sélectionner**.
   
    ![newadduser](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Portail Azure Classic**

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/).
2. Dans le panneau de navigation, sélectionnez **Paramètres**> **Administrateurs**> **Ajouter**. </br>
   
    ![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Entrez l’adresse de messagerie de la personne que vous souhaitez ajouter en tant que coadministrateur, puis sélectionnez l’abonnement auquel le coadministrateur doit avoir accès.</br>
   
    ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

L’adresse de messagerie suivante peut être ajoutée en tant que coadministrateur :

* **compte Microsoft** (anciennement Windows Live ID) </br>
   Vous pouvez utiliser un compte Microsoft pour vous connecter à tous les produits et services cloud Microsoft orientés utilisateur, notamment Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone et Xbox LIVE.
* **Organizational account**</br>
   Un compte professionnel est un compte créé sous Azure Active Directory. L’adresse du compte professionnel est similaire à ceci : user@&lt;votredomaine&gt;.onmicrosoft.com

### <a name="limitations-and-restrictions"></a>Limitations et restrictions
* Chaque abonnement est associé à un répertoire Azure AD (également appelé « répertoire par défaut »). Pour identifier le répertoire par défaut associé à l’abonnement, connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/), sélectionnez **Paramètres** > **Abonnements**. Vérifiez l’ID d’abonnement pour trouver le répertoire par défaut.
* Si vous vous êtes connecté avec un compte Microsoft, vous ne pouvez ajouter d’autres comptes ou utilisateurs Microsoft dans l’annuaire par défaut qu’en tant que coadministrateur.
* Si vous vous êtes connecté avec un compte professionnel, vous pouvez ajouter d’autres comptes professionnels de votre organisation en tant que coadministrateur. Par exemple, abby@contoso.com peut ajouter bob@contoso.com en tant qu’administrateur de services fédérés ou coadministrateur, mais ne peut pas ajouter john@notcontoso.com, sauf si john@noncontoso.com est l’utilisateur de l’annuaire par défaut. Les utilisateurs connectés avec des comptes professionnels peuvent continuer à ajouter des utilisateurs de compte Microsoft en tant qu’administrateur de services fédérés ou coadministrateur.
* Il est désormais possible de se connecter à Azure avec un compte professionnel. Voici les modifications apportées aux exigences liées aux comptes Administrateur de services fédérés et Coadministrateur :
  
  | Méthode de connexion | Ajouter un compte ou des utilisateurs Microsoft dans l’annuaire par défaut en tant que coadministrateur ou administrateur de services fédérés ? | Ajouter un compte de société dans la même organisation que le coadministrateur ou administrateur de services fédérés ? | Ajouter un compte de société dans une autre organisation que le coadministrateur ou administrateur de services fédérés ? |
  | --- | --- | --- | --- |
  |  Compte Microsoft |Oui |Non |Non |
  |  Compte de société |Oui |Oui |Non |

## <a name="how-to-change-service-administrator-for-a-subscription"></a>Comment modifier l’administrateur de services fédérés d’un abonnement
Seul l’administrateur de compte peut modifier l’administrateur de services fédérés d’un abonnement.

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/subscriptions) en utilisant l’administrateur de compte.
2. Sélectionnez l’abonnement que vous souhaitez modifier.
3. Sur le côté droit, cliquez sur **Modifier les détails de l’abonnement** . </br>
   
    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. Dans la zone **ADMINISTRATEUR DE SERVICES** , entrez l’adresse de messagerie du nouvel administrateur de services fédérés. </br>
   
    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="how-to-change-the-account-administrator"></a>Modification de l’administrateur de compte
Pour transférer la propriété du compte Azure vers un autre compte, consultez [Transfert de propriété d’un abonnement Azure](billing-subscription-transfer.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](active-directory/active-directory-understanding-resource-access.md)
* Pour plus d’informations sur l’association entre Azure Active Directory et votre abonnement Azure, consultez [Association des abonnements Azure avec Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)
* Pour plus d’informations sur le lien entre Azure Active Directory et votre abonnement Azure, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory/active-directory-assign-admin-roles.md)

> [!NOTE]
> Si vous avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
> 
> 




<!--HONumber=Nov16_HO3-->


