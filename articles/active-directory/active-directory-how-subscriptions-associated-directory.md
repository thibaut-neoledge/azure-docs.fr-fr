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
ms.date: 07/24/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 7a82dcddcd051c88c2a6a515ed2353961307182a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Association des abonnements Azure avec Azure Active Directory
Cet article vous indique comment vous connecter à Microsoft Azure et aborde des problèmes connexes, tels que la relation entre un abonnement Azure et Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Microsoft recommande de gérer Azure AD à l’aide du [Centre d’administration Azure AD](https://aad.portal.azure.com) dans le portail Azure au lieu d’utiliser le portail Azure classique référencé dans cet article.

## <a name="accounts-that-you-can-use-to-sign-in"></a>Comptes que vous pouvez utiliser pour vous connecter
Commençons tout d’abord par les comptes que vous pouvez utiliser pour vous connecter. Il en existe deux types : le compte Microsoft (anciennement Microsoft Live ID) et le compte professionnel ou scolaire, qui est un compte stocké dans Azure AD.

| Compte Microsoft | Compte Azure AD |
| --- | --- |
| Le système d'identité de consommateurs exécuté par Microsoft |Le système d'identité d’entreprises exécuté par Microsoft |
| Authentification des services orientés vers les consommateurs, tels que Hotmail et MSN |Authentification des services orientés vers les entreprises, tels que Office 365 |
| Les consommateurs créent leurs propres comptes Microsoft, par exemple lors de leur inscription au courrier électronique |Les sociétés et organisations créent et gèrent leurs propres comptes professionnels ou scolaires |
| Les identités sont créées et stockées dans le système de comptes Microsoft |Les identités sont créées via Azure ou un autre service, tel qu’Office 365, puis sont stockées dans une instance Azure AD attribuée à l’organisation |

Même si Azure avait initialement autorisé l’accès aux utilisateurs de compte Microsoft uniquement, il autorise désormais l’accès aux utilisateurs des *deux* systèmes. Ceci a été possible via l’approbation des propriétés Azure pour l’authentification Azure AD, l’authentification Azure AD des utilisateurs professionnels et la création d’une relation de fédération dans laquelle Azure AD approuve le système d’identité Microsoft de compte consommateur pour authentifier les utilisateurs consommateurs. Par conséquent, Azure AD est en mesure d'authentifier les comptes « invités » de Microsoft, ainsi que les comptes Azure AD « natifs ».

Par exemple, un utilisateur titulaire d’un compte Microsoft se connecte au portail Azure Classic.

> [!NOTE]
> Pour vous connecter au portail Azure Classic, msmith@hotmail.com doit disposer d’un abonnement Azure. Le compte doit être un administrateur de services fédérés ou un coadministrateur de l'abonnement.
>
>

![][1]

Du fait que cette adresse Hotmail soit un compte consommateur, la connexion est authentifiée via le système d'identité de consommateurs de Microsoft. Le système d'identité Azure AD approuve l'authentification effectuée par le système de comptes Microsoft et émettra un jeton pour accéder aux services Azure.

## <a name="how-an-azure-subscription-is-related-to-azure-ad"></a>Association d’un abonnement Azure à Azure AD
Chaque abonnement Azure dispose d’une relation d’approbation avec une instance Azure AD. Cela signifie qu'il approuve ce répertoire pour authentifier les utilisateurs, les services et les appareils. Plusieurs abonnements peuvent approuver le même répertoire, mais un abonnement n’approuve qu’un seul répertoire. Vous pouvez découvrir quel répertoire est approuvé par votre abonnement dans l'onglet Paramètres. Vous pouvez [modifier les paramètres d'abonnement](active-directory-understanding-resource-access.md) pour modifier le répertoire qu'il approuve.

Cette relation de confiance qu’un abonnement possède avec un répertoire est contraire à celle établie entre un abonnement et toutes les autres ressources Azure (sites Web, bases de données, etc.), qui se rapprochent plus des ressources enfants d'un abonnement. Lorsqu’un abonnement expire, les autres ressources associées à l'abonnement deviennent également inaccessibles. Mais le répertoire reste dans Azure, et vous pouvez associer un autre abonnement à ce répertoire et continuer à gérer les utilisateurs du répertoire.

De même, l’extension Azure AD que vous apercevez dans votre abonnement ne fonctionne pas de la même manière que les autres extensions du portail Azure Classic. Les autres extensions du portail Azure Classic sont limitées à l’abonnement Azure. Ce que vous apercevez dans l’extension Azure AD ne varie pas en fonction de l’abonnement : seuls les répertoires sont affichés en fonction de l’utilisateur connecté.

Tous les utilisateurs disposent d'un répertoire de base unique qui les authentifie, mais ils peuvent également être invités dans d'autres répertoires. Dans l’extension Azure AD, vous découvrirez tous les répertoires auxquels votre compte d’utilisateur ne s’est pas abonné. Tous les répertoires auxquels votre compte d’utilisateur ne s’est pas abonné ne s’afficheront pas. Un répertoire peut émettre des jetons pour des comptes professionnels ou scolaires dans Azure AD ou des comptes utilisateurs Microsoft (car Azure AD est fédéré avec le système de comptes Microsoft).

Ce diagramme présente un abonnement de Michael Smith après que celui-ci s’est connecté à l’aide d’un compte de Contoso.

![][2]

## <a name="how-to-manage-a-subscription-and-a-directory"></a>Gestion d’un abonnement et d’un répertoire
Les rôles administratifs d’un abonnement Azure gèrent les ressources liées à l'abonnement Azure. Ces rôles et bonnes pratiques de gestion de votre abonnement sont abordés dans la section [Affectation des rôles administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

Le rôle d'administrateur de service vous est, par défaut, attribué dès lors que vous vous abonnez. Si d'autres personnes doivent se connecter et accéder aux services à l'aide de l'abonnement, vous pouvez les ajouter en tant que coadministrateurs. L’administrateur et les coadministrateurs de service peuvent être des comptes Microsoft ou des comptes professionnels ou scolaires provenant du répertoire auquel l’abonnement Azure est associé.

Azure AD dispose d’un autre ensemble de rôles administratifs qui permet de gérer le répertoire et les fonctionnalités liées à l'identité. Par exemple, l'administrateur général d'un répertoire peut ajouter des utilisateurs et des groupes dans le répertoire ou requérir l'authentification multi-facteur des utilisateurs. Un utilisateur qui crée un répertoire est affecté au rôle d’administrateur général et il peut attribuer des rôles d'administrateur à d'autres utilisateurs.

De même qu’avec les administrateurs d’abonnements, les rôles d'administrateur Azure AD peuvent être des comptes Microsoft ou bien des comptes professionnels ou scolaires. Les rôles administratifs Azure AD sont également utilisés par d'autres services tels que Office 365 et Microsoft Intune. Pour en savoir plus, consultez la rubrique [Attribution des rôles d'administrateur](active-directory-assign-admin-roles.md).

À noter que les administrateurs d'abonnement Azure et les administrateurs de répertoires Azure AD sont deux concepts bien différents. Les administrateurs d’abonnement Azure peuvent gérer des ressources dans Azure et afficher l’extension Active Directory dans le portail Azure Classic (car le portail de gestion est une ressource Azure Classic). Les administrateurs de répertoires peuvent gérer des propriétés dans le répertoire.

Une personne peut se voir attribuer deux rôles, mais ceci n'est en aucun cas obligatoire. Un utilisateur peut être affecté au rôle d’administrateur général de répertoires mais il ne peut pas être administrateur de services ou coadministrateur d'un abonnement Azure. S’il n’est pas administrateur de l’abonnement, cet utilisateur ne peut pas se connecter au portail Azure Classic. Mais l'utilisateur peut effectuer des tâches d'administration de répertoires à l'aide d'autres outils tels que Azure AD PowerShell ou le centre d'administration Office 365.

## <a name="why-cant-i-manage-the-directory-with-my-current-user-account"></a>Pourquoi ne puis-je pas gérer le répertoire via mon compte d'utilisateur actuel ?
Il se peut parfois qu’un utilisateur tente de se connecter au portail Azure Classic à l’aide d’un compte professionnel ou scolaire avant de souscrire un abonnement Azure. Dans ce cas, l’utilisateur recevra un message lui indiquant qu’il n’existe aucun abonnement pour ce compte. Le message inclut un lien permettant de démarrer un abonnement à la version d’évaluation gratuite.

Après s’être abonné à la version d’évaluation gratuite, l’utilisateur découvrira le répertoire de l’organisation dans le portail Azure Classic, mais il ne pourra pas le gérer (c’est-à-dire qu’il ne pourra pas ajouter d’utilisateurs, ou modifier des propriétés d’utilisateur existantes), car l’utilisateur n’est pas un administrateur général de répertoires . L’abonnement permet à l’utilisateur d’utiliser le portail Azure Classic et de voir l’extension Active Directory, mais des autorisations supplémentaires d’un administrateur général sont nécessaires pour gérer le répertoire.

## <a name="using-your-work-or-school-account-to-manage-an-azure-subscription-that-was-created-by-using-a-microsoft-account"></a>Utiliser votre compte professionnel ou scolaire pour gérer un abonnement Azure qui a été créé à l’aide d’un compte Microsoft
Nous vous recommandons de [vous inscrire à Azure en tant qu'organisation](sign-up-organization.md) et d’utiliser un compte professionnel ou scolaire pour gérer les ressources dans Azure. Des comptes professionnels ou scolaires sont préférables, car ils peuvent être gérés de manière centralisée par l'organisation qui les a émis. Ils possèdent plus de fonctionnalités que les comptes Microsoft et sont directement authentifiés par Azure AD. Ce compte permet d'accéder à d'autres services Microsoft en ligne qui sont offerts aux entreprises et organisations, tels que Office 365 ou Microsoft Intune. Si vous possédez déjà un compte que vous utilisez avec ces autres propriétés, vous préférerez certainement utiliser ce même compte Azure.  Vous disposez également d’une instance Active Directory qui sauvegarde ces propriétés que vous souhaiterez que votre abonnement Azure approuve.

En outre, vous pouvez gérer des comptes professionnels ou scolaires de bien plus de manières qu’un compte Microsoft. Un administrateur peut, par exemple, réinitialiser le mot de passe d'un compte professionnel ou scolaire, ou requérir pour lui l'authentification multi-facteur.

Vous souhaiterez peut-être, dans certains cas, qu’un utilisateur de votre organisation puisse gérer les ressources qui sont associées à un abonnement Azure pour un compte consommateur Microsoft. Pour plus d'informations sur le fait d'avoir différents comptes permettant de gérer des abonnements ou des répertoires, consultez la rubrique [Gérer le répertoire de votre abonnement Office 365 dans Azure](#manage-the-directory-for-your-office-365-subscription-in-azure).

## <a name="signing-in-when-you-used-your-work-email-for-your-microsoft-account"></a>Vous connecter lorsque vous utilisez votre adresse de messagerie professionnelle pour votre compte Microsoft
Si vous avez créé, à un moment donné, un compte consommateur Microsoft à l'aide de votre adresse de messagerie professionnelle comme identifiant utilisateur, vous pouvez apercevoir une page vous invitant à sélectionner le système de comptes Microsoft Azure ou le système de comptes Microsoft.

![][3]

Vous disposez, sous le même nom, de comptes utilisateur, un dans Azure AD et l'autre dans le système de comptes Microsoft consommateur. Vous devez sélectionner le compte qui est associé à l'abonnement Azure que vous souhaitez utiliser. Si une erreur s’affiche et vous indique qu'un abonnement n'existe pas pour cet utilisateur, cela veut certainement dire que vous avez sélectionné la mauvaise option. Déconnectez-vous et réessayez. Pour découvrir les raisons pour lesquelles vous ne parvenez pas à vous connecter, consultez la rubrique [Résolutions des problèmes vous indiquant qu’il est « impossible de trouver un abonnement associé à votre compte »](https://social.msdn.microsoft.com/Forums/en-US/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement).

## <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Gérer le répertoire de votre abonnement Office 365 dans Azure
Supposons que vous vous êtes abonné à Office 365 avant de vous inscrire à Azure. Vous souhaitez désormais gérer le répertoire de l’abonnement Office 365 dans le portail Azure Classic. Pour ce faire, deux solutions possibles, qui dépendent du fait que vous vous êtes abonné ou non à Azure.

### <a name="i-do-not-have-a-subscription-for-azure"></a>Je ne dispose pas d’un abonnement Azure
Il vous suffit, dans ce cas, de vous [inscrire à Azure](sign-up-organization.md) à l’aide du compte professionnel ou scolaire que vous utilisez pour vous connecter à Office 365. Les informations pertinentes du compte Office 365 sont préremplies dans le formulaire d'abonnement d’Azure. Votre compte sera affecté au rôle d'administrateur de service de l'abonnement.  

### <a name="i-do-have-a-subscription-for-azure-using-my-microsoft-account"></a>Je dispose d’un abonnement Azure via mon compte Microsoft
Si vous vous êtes abonné à Office 365 avec un compte professionnel ou scolaire, puis à Azure avec un compte Microsoft, vous disposez de deux répertoires : un pour votre organisation ou votre établissement scolaire, et un répertoire par défaut, créé lors de votre abonnement à Azure.

Pour gérer les deux répertoires dans le portail Azure Classic, procédez comme suit.

> [!NOTE]
> Ces étapes ne peuvent être effectuées que lorsqu’un utilisateur est connecté via un compte Microsoft. Lorsque l’utilisateur est connecté avec un  compte professionnel ou scolaire, l’option **Utiliser un répertoire existant** n’est pas disponible, car un compte professionnel ou scolaire ne peut être authentifié qu’à l’aide d’un répertoire de base (autrement dit, le répertoire du compte professionnel ou scolaire, qui est détenu par l’organisation ou l’établissement scolaire).
>
>

1. Connectez-vous au portail Azure Classic à l’aide de votre compte Microsoft.
2. Cliquez sur **Nouveau** > **App Services** > **Active Directory** > **Répertoire** > **Création personnalisée**.
3. Cliquez sur **Utiliser un répertoire existant**, sur **Je suis prêt à être déconnecté**, puis sur la coche pour terminer l'action.
4. Connectez-vous au portail Azure Classic à l’aide d’un compte disposant des droits d’administrateur général du répertoire Professionnel ou scolaire.
5. Lorsque vous êtes invité à **Utiliser le répertoire Contoso avec Azure**, cliquez sur **Continuer**.
6. Cliquez sur **Se déconnecter maintenant**.
7. Reconnectez-vous au portail Azure Classic à l’aide de votre compte Microsoft. Les répertoires apparaîtront dans l'extension Active Directory.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la façon de modifier les administrateurs pour un abonnement Azure, voir [Comment ajouter ou modifier les rôles d’administrateur Azure](../billing/billing-add-change-azure-subscription-administrator.md)
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](active-directory-understanding-resource-access.md)
* Pour plus d’informations sur l’attribution des rôles dans Azure AD, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

