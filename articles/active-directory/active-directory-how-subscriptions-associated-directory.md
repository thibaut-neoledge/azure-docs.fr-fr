---
title: Association des abonnements Azure avec Azure Active Directory | Microsoft Docs
description: "Connexion à Microsoft Azure et problèmes connexes, tels que la relation entre un abonnement Azure et Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: b2f8cc774b6cc245a4000e5c6924a8bb817707bc
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Association des abonnements Azure avec Azure Active Directory
Cet article fournit des informations sur la relation entre un abonnement Azure et Azure Active Directory (Azure AD).

## <a name="your-azure-subscriptions-relationship-to-azure-ad"></a>Relation de votre abonnement Azure à Azure AD
Votre abonnement Azure possède une relation d’approbation avec Azure AD, ce qui signifie qu’il fait confiance à l’annuaire pour authentifier les utilisateurs, les services et les périphériques. Plusieurs abonnements peuvent faire confiance au même répertoire, mais un abonnement ne fait confiance qu’à un seul répertoire. 

La relation d’approbation dont dispose un abonnement avec un répertoire diffère de la relation qu’il possède avec d’autres ressources dans Azure (sites web, bases de données etc.). En cas d’expiration d’un abonnement, l’accès aux autres ressources associées à cet abonnement s’arrête également. En revanche, le répertoire reste dans Azure. Vous pouvez associer un autre abonnement à ce répertoire et continuer à gérer les utilisateurs du répertoire.

![diagramme relatif à l’association des abonnements](./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png)

Azure AD ne fonctionne pas comme les autres services dans votre abonnement Azure. D’autres services Azure sont subordonnés à l’abonnement Azure. Cependant, ce qui est affiché dans Azure AD ne varie pas en fonction de l’abonnement. Il autorise l’accès aux répertoires en fonction de l’utilisateur connecté.

Tous les utilisateurs disposent d'un répertoire de base unique qui les authentifie, mais ils peuvent également être invités dans d'autres répertoires. Dans Azure AD, vous ne pouvez voir que les répertoires dont est membre votre compte d’utilisateur. Un répertoire peut également être synchronisé avec l'annuaire Active Directory local.

## <a name="azure-ad-and-cloud-service-subscriptions"></a>Azure AD et les abonnements de service cloud
Azure AD fournit l’annuaire principal et les fonctionnalités de gestion d’identité qui existent derrière la plupart des services cloud Microsoft, y compris :

* Les tables Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Quand vous vous inscrivez à ces services de cloud computing Microsoft, vous obtenez gratuitement le service Azure AD. Il n’est possible d’ajouter un nouvel abonnement Azure à un répertoire Azure AD que si vous êtes connecté avec un compte Microsoft. Par exemple, si vous vous inscrivez à Azure à l’aide d’un compte Microsoft et que vous vous inscrivez également à un autre service cloud Microsoft à l’aide d’un compte professionnel ou scolaire, vous disposez de deux instances d’Azure AD :
1. Le répertoire par défaut de votre abonnement Azure. Vous pouvez ajouter un autre abonnement Azure à ce répertoire ou à d’autres si vous êtes connecté avec un compte Microsoft, car vous pouvez être authentifié par Azure.
2. Le répertoire de base de votre compte professionnel ou scolaire. Si vous êtes connecté à Azure avec un compte professionnel ou scolaire, il est impossible d’ajouter un abonnement Azure à un répertoire existant, car votre compte professionnel ou scolaire ne peut pas être authentifié directement par Azure. 
 
Pour en savoir plus sur la façon de modifier les administrateurs d’un abonnement Azure, consultez [Transférer la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md)

## <a name="suggestions-to-manage-both-a-subscription-and-a-directory"></a>Suggestions pour gérer un abonnement et un répertoire
Les rôles administratifs d’un abonnement Azure gèrent les ressources liées à l'abonnement Azure. Cette section explique les différences entre les administrateurs d’abonnements Azure et les administrateurs d’annuaires Azure AD. Les rôles d’administration et les autres suggestions permettant de les utiliser pour gérer votre abonnement sont expliqués dans [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

Le rôle d'administrateur de service vous est, par défaut, attribué dès lors que vous vous abonnez. Si d'autres personnes doivent se connecter et accéder aux services à l'aide de l'abonnement, vous pouvez les ajouter en tant que coadministrateurs. 

Azure AD dispose d’un autre ensemble de rôles administratifs qui permet de gérer le répertoire et les fonctionnalités liées à l'identité. Par exemple, l'administrateur général d'un répertoire peut ajouter des utilisateurs et des groupes dans le répertoire ou requérir l'authentification multi-facteur des utilisateurs. Un utilisateur qui crée un répertoire est affecté au rôle d’administrateur général et peut attribuer des rôles d'administration à d'autres utilisateurs. Les rôles administratifs Azure AD sont également utilisés par d'autres services tels que Office 365 et Microsoft Intune. 

Les administrateurs d'abonnement Azure et les administrateurs de répertoires Azure AD possèdent deux rôles bien différents. 
* Les administrateurs d’abonnement Azure peuvent gérer des ressources dans Azure et utiliser Azure AD dans le portail Azure (car le portail Azure est lui-même une ressource Azure). 
* Les administrateurs de répertoires ne peuvent gérer des propriétés que dans le répertoire Azure AD.

Une personne peut se voir attribuer deux rôles, mais ceci n'est en aucun cas obligatoire. Un utilisateur peut être affecté au rôle d’administrateur général de répertoires mais il ne peut pas être administrateur de services ou coadministrateur d'un abonnement Azure. L’utilisateur peut se connecter au portail Azure sans être un administrateur de l’abonnement, mais il ne peut pas gérer les répertoires de cet abonnement dans le portail. Cet utilisateur peut gérer des répertoires à l’aide d’autres outils tels que Azure AD PowerShell ou le Centre d’administration Office 365.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la façon de modifier les administrateurs d’un abonnement Azure, consultez [Transférer la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md)
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](active-directory-understanding-resource-access.md)
* Pour plus d’informations sur l’attribution des rôles dans Azure AD, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

