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
ms.date: 08/24/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 283c9903501a1e497e4dde81146d21edb869e9e2
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Association des abonnements Azure avec Azure Active Directory
Cet article vous fournit des informations sur la relation entre un abonnement Azure et Azure Active Directory (Azure AD), et vous explique comment ajouter un abonnement existant à votre répertoire Azure AD.

## <a name="your-azure-subscriptions-relationship-to-azure-ad"></a>Relation de votre abonnement Azure à Azure AD
Votre abonnement Azure possède une relation d’approbation avec Azure AD, ce qui signifie qu’il fait confiance à l’annuaire pour authentifier les utilisateurs, les services et les périphériques. Plusieurs abonnements peuvent faire confiance au même répertoire, mais un abonnement ne fait confiance qu’à un seul répertoire. 

La relation d’approbation dont dispose un abonnement avec un répertoire diffère de la relation qu’il possède avec d’autres ressources dans Azure (sites web, bases de données etc.). En cas d’expiration d’un abonnement, l’accès aux autres ressources associées à cet abonnement s’arrête également. En revanche, un répertoire Azure AD reste dans Azure. Vous pouvez associer un autre abonnement à ce répertoire et le gérer à l’aide d’un autre abonnement.

![diagramme relatif à l’association des abonnements](./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png)

Tous les utilisateurs disposent d'un répertoire de base unique qui les authentifie, mais ils peuvent également être invités dans d'autres répertoires. Dans Azure AD, vous pouvez voir les répertoires dont votre compte d’utilisateur est membre ou invité.

## <a name="azure-ad-and-cloud-service-subscriptions"></a>Azure AD et les abonnements de service cloud
Azure AD fournit l’annuaire principal et les fonctionnalités de gestion d’identité qui existent derrière la plupart des services cloud Microsoft, y compris :

* Les tables Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Quand vous vous inscrivez à ces services de cloud computing Microsoft, vous obtenez gratuitement le service Azure AD. Il n’est possible d’ajouter un nouvel abonnement Azure à un répertoire Azure AD que si vous êtes connecté avec un compte Microsoft. Si vous êtes connecté à Azure avec un compte professionnel ou scolaire, il est impossible d’ajouter un abonnement Azure à un répertoire existant, car votre compte professionnel ou scolaire ne peut pas être authentifié directement par Azure. 

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Pour ajouter un abonnement existant à votre répertoire Azure AD
Vous devez vous connecter avec un compte qui existe à la fois dans le répertoire actif auquel l’abonnement est associé et dans le répertoire dans lequel vous souhaitez l’ajouter. 

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/Home/Index) avec un compte qui est l’administrateur du compte de l’abonnement et dont vous souhaitez transférer la propriété de l’abonnement.
2. Vérifiez que l’utilisateur dont vous voulez être le propriétaire de l’abonnement est dans le répertoire cible.
3. Cliquez sur **Transférer la propriété de l’abonnement**.
4. Spécifiez le destinataire. Le destinataire reçoit automatiquement un e-mail contenant un lien d’acceptation.
5. Le destinataire clique sur le lien et suit les instructions, notamment pour la saisie des informations de paiement. Lorsque le destinataire a terminé, l’abonnement est transféré. 
6. Le répertoire par défaut de l’abonnement est modifié pour être remplacé par le répertoire contenant l’utilisateur.


## <a name="suggestions-to-manage-both-a-subscription-and-a-directory"></a>Suggestions pour gérer un abonnement et un répertoire
Les rôles administratifs d’un abonnement Azure gèrent les ressources liées à l'abonnement Azure. Cette section explique les différences entre les administrateurs d’abonnements Azure et les administrateurs d’annuaires Azure AD. Les rôles d’administration et les autres suggestions permettant de les utiliser pour gérer votre abonnement sont expliqués dans [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

Le rôle d'administrateur de service vous est, par défaut, attribué dès lors que vous vous abonnez. Si d'autres personnes doivent se connecter et accéder aux services à l'aide de l'abonnement, vous pouvez les ajouter en tant que coadministrateurs. 

Azure AD dispose d’un autre ensemble de rôles administratifs qui permet de gérer le répertoire et les fonctionnalités liées à l'identité. Par exemple, l'administrateur général d'un répertoire peut ajouter des utilisateurs et des groupes dans le répertoire ou requérir l'authentification multi-facteur des utilisateurs. Un utilisateur qui crée un répertoire est affecté au rôle d’administrateur général et peut attribuer des rôles d'administration à d'autres utilisateurs. Les rôles administratifs Azure AD sont également utilisés par d'autres services tels que Office 365 et Microsoft Intune. 

Les administrateurs d'abonnement Azure et les administrateurs de répertoires Azure AD possèdent deux rôles bien différents. 
* Les administrateurs d’abonnement Azure peuvent gérer des ressources dans Azure et utiliser Azure AD dans le portail Azure (car le portail Azure est lui-même une ressource Azure). 
* Les administrateurs de répertoires ne peuvent gérer des propriétés que dans le répertoire Azure AD.

Une personne peut se voir attribuer deux rôles, mais ceci n'est en aucun cas obligatoire. Un administrateur général de répertoires peut ne pas être administrateur de services ou coadministrateur d’un abonnement Azure, et inversement. L’utilisateur peut se connecter au portail Azure sans être un administrateur de l’abonnement, mais il ne peut pas gérer les répertoires de cet abonnement dans le portail. Toutefois, cet utilisateur peut gérer des répertoires à l’aide d’autres outils tels que Azure AD PowerShell ou le Centre d’administration Office 365.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la façon de modifier les administrateurs d’un abonnement Azure, consultez [Transférer la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md)
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](active-directory-understanding-resource-access.md)
* Pour plus d’informations sur l’attribution des rôles dans Azure AD, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

