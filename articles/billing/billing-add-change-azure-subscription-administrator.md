---
title: "Ajout ou modification de rôles d’abonnement administrateur Azure | Microsoft Docs"
description: "Décrit comment ajouter ou modifier un coadministrateur, administrateur de services et administrateur de compte Azure"
services: 
documentationcenter: 
author: genlin
manager: vikdesai
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 6d438cde704323a07cc125eb75d08cb64b2432ec
ms.openlocfilehash: 00b71701c4c114007e2d9a6d23f20b3df5fb2534
ms.lasthandoff: 02/21/2017


---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Ajout ou modification de rôles d’administrateur Azure gérant l’abonnement ou les services
Vous pouvez modifier l’administrateur Azure qui gère votre abonnement Azure ou les services Azure utilisés dans votre abonnement. Pour afficher les informations de facturation Azure et gérer les abonnements, vous devez vous connecter au [Centre des comptes](https://account.windowsazure.com/Home/Index) en tant qu’administrateur de compte. 

## <a name="add-an-admin-for-a-subscription"></a>Ajout d’un administrateur à un abonnement
Vous pouvez ajouter un administrateur Azure dans le portail Azure ou dans le portail Azure Classic.

**portail Azure**

Pour ajouter une personne en tant qu’administrateur d’un abonnement dans le Portail Azure, vous lui donnez le rôle de propriétaire. Le rôle de propriétaire peut uniquement gérer les ressources de l’abonnement que vous avez affecté. Il n’a pas de privilèges d’accès à d’autres abonnements. Les propriétaires ajoutés au moyen du [Portail Azure](https://portal.azure.com) ne peuvent pas gérer les ressources dans le [Portail Azure Classic](https://manage.windowsazure.com).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Abonnement** > *l’abonnement auquel l’administrateur doit accéder*.

    ![Capture d’écran montrant l’abonnement sélectionné](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)

3. Dans le panneau de l’abonnement, sélectionnez **Contrôle d’accès (IAM)**> **Ajouter**.

    ![Capture d’écran montrant le bouton Ajouter sélectionné](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. Sélectionnez **Sélectionner un rôle** > **Propriétaire**.

    ![Capture d’écran montrant le rôle Propriétaire sélectionné](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)

5. Entrez l’adresse de messagerie de l’utilisateur que vous souhaitez ajouter en tant que propriétaire, cliquez sur l’utilisateur, puis sur **Sélectionner**.

    ![Capture d’écran montrant un e-mail d’utilisateur ajouté et sélectionné](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Portail Azure Classic**

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/).
2. Dans le panneau de navigation, sélectionnez **Paramètres**> **Administrateurs**> **Ajouter**. </br>

    ![Capture d’écran montrant comment obtenir le bouton Ajouter](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Entrez l’adresse de messagerie de la personne que vous souhaitez ajouter en tant que coadministrateur, puis sélectionnez l’abonnement auquel le coadministrateur doit avoir accès.</br>

    ![Capture d’écran montrant un abonnement sélectionné ](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

L’adresse de messagerie suivante peut être ajoutée en tant que coadministrateur :

* **compte Microsoft** (anciennement Windows Live ID) </br>
  Vous pouvez utiliser un compte Microsoft pour vous connecter à tous les produits et services cloud Microsoft orientés utilisateur, notamment Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone et Xbox LIVE.
* **Organizational account**</br>
  Un compte professionnel est un compte créé sous Azure Active Directory. L’adresse du compte professionnel a ce format :

    utilisateur@&lt;votre domaine&gt;.onmicrosoft.com



## <a name="change-service-administrator-for-a-subscription"></a>Modification de l’administrateur de services fédérés d’un abonnement
Seul l’administrateur de compte peut modifier l’administrateur de services fédérés d’un abonnement.

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/subscriptions) en utilisant l’administrateur de compte.
2. Sélectionnez l’abonnement que vous souhaitez modifier.
3. Sur le côté droit, cliquez sur **Modifier les détails de l’abonnement** . </br>

    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. Dans la zone **ADMINISTRATEUR DE SERVICES** , entrez l’adresse de messagerie du nouvel administrateur de services fédérés. </br>

    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="change-the-account-administrator"></a>Modification de l’administrateur de compte
Pour transférer la propriété du compte Azure vers un autre compte, consultez [Transfert de propriété d’un abonnement Azure](billing-subscription-transfer.md).

Nous vous recommandons fortement de ne pas supprimer ou renommer l’adresse de messagerie de l’administrateur de compte. Vous pourriez constater un comportement inattendu et indésirable avec le compte Azure. Vous pourriez ne plus être en mesure de vous connecter à Azure avec ce compte, apporter des modifications au compte ou gérer des ressources avec ce compte. 

## <a name="check-the-account-administrator-of-the-subscription"></a>Vérification de l’administrateur de compte de l’abonnement
Si vous ne savez pas qui est l’administrateur de compte de votre abonnement, procédez comme suit pour le découvrir.

  1. Connectez-vous au [portail Azure](https://portal.azure.com).
  2. Dans le menu Hub, sélectionnez **Abonnement**.
  3. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
  4. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .  

## <a name="types-of-azure-admin-accounts"></a>Types de comptes d’administrateur Azure
 Les trois types de rôles Administrateur dans Microsoft Azure sont Administrateur de compte, Administrateur de services fédérés et Coadministrateur. Le tableau suivant décrit la différence entre ces trois rôles Administrateur.

| Rôle administratif | Limite | Description |
| --- | --- | --- |
| Administrateur de compte |1 par compte Azure |Il s’agit de la personne qui a souscrit ou acheté des abonnements Azure et qui est autorisée à accéder au [Centre des comptes](https://account.windowsazure.com/Home/Index) et à effectuer diverses tâches de gestion. Ces tâches incluent la possibilité de créer et d’annuler des abonnements, de modifier la facturation d’un abonnement et de modifier l’administrateur de services fédérés. |
| Administrateur de services fédérés |1 par abonnement Azure |Ce rôle est autorisé à gérer les services sur le [portail Azure](https://portal.azure.com). Par défaut, pour un nouvel abonnement, l’administrateur de compte est également l’administrateur de services fédérés. |
| Coadministrateur (CA) dans le [portail Azure Classic](https://manage.windowsazure.com) |200 par abonnement |Ce rôle possède les mêmes privilèges d’accès que l’administrateur de services fédérés, à ceci près qu’il ne peut pas modifier la manière dont les abonnements sont associés aux répertoires Azure. |

Le contrôle d’accès en fonction du rôle (RBAC) Azure Active Directory permet d’ajouter les utilisateurs à plusieurs rôles. Pour plus d’informations, consultez la rubrique [Contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md).

## <a name="limitations-and-restrictions-for-admin-accounts"></a>Limitations et restrictions des comptes Administrateur
* Chaque abonnement est associé à un répertoire Azure AD (également appelé « répertoire par défaut »). Pour identifier le répertoire par défaut associé à l’abonnement, connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/), sélectionnez **Paramètres** > **Abonnements**. Vérifiez l’ID d’abonnement pour trouver le répertoire par défaut.
* Si vous êtes connecté avec un compte Microsoft, vous ne pouvez ajouter d’autres comptes ou utilisateurs Microsoft dans l’annuaire par défaut qu’en tant que coadministrateur.
* Si vous êtes connecté avec un compte professionnel, vous pouvez ajouter d’autres comptes professionnels de votre organisation en tant que coadministrateur. Par exemple, abby@contoso.com peut ajouter bob@contoso.com en tant qu’administrateur de services fédérés ou coadministrateur, mais ne peut pas ajouter john@notcontoso.com, sauf si john@noncontoso.com est l’annuaire par défaut. Les utilisateurs connectés avec des comptes professionnels peuvent continuer à ajouter des utilisateurs de compte Microsoft en tant qu’administrateur de services fédérés ou coadministrateur.
* Il est désormais possible de se connecter à Azure avec un compte professionnel. Voici les modifications apportées aux exigences liées aux comptes Administrateur de services fédérés et Coadministrateur :

  | Méthode de connexion | Ajouter un compte ou des utilisateurs Microsoft dans l’annuaire par défaut en tant que coadministrateur ou administrateur de services fédérés ? | Ajouter un compte de société dans la même organisation que le coadministrateur ou administrateur de services fédérés ? | Ajouter un compte de société dans une autre organisation que le coadministrateur ou administrateur de services fédérés ? |
  | --- | --- | --- | --- |
  |  Compte Microsoft |Oui |Non |Non |
  |  Compte de société |Oui |Oui |Non |

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>En savoir plus sur le contrôle d’accès aux ressources et Active Directory
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, consultez la page[Présentation de l’accès aux ressources dans Azure](../active-directory/active-directory-understanding-resource-access.md).
* Pour plus d’informations sur Azure Active Directory, consultez les pages [Association des abonnements Azure avec Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) et [Affecter des rôles Administrateur dans Azure Active Directory](../active-directory/active-directory-assign-admin-roles.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

