---
title: "Ajouter des utilisateurs à partir d’autres répertoires ou entreprises partenaires dans Azure Active Directory | Microsoft Docs"
description: "Explique comment ajouter des utilisateurs ou modifier les informations utilisateur dans Azure Active Directory, y compris celles des utilisateurs externes et invités."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 564a04ec-53c1-470b-9ab9-f3db57da0a89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 7b506668b3025774edec25b6a37b83a47d702cb0
ms.openlocfilehash: b2eaddc631087d8b7b94e40f508bdd5da5229736


---
# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory"></a>Ajouter des utilisateurs à partir d’autres répertoires ou entreprises partenaires dans Azure Active Directory
> [!div class="op_single_selector"]
> * [Portail Azure](active-directory-users-create-external-azure-portal.md)
> * [Portail Azure Classic](active-directory-create-users-external.md)
>
>

Cet article explique comment ajouter des utilisateurs à partir d’autres répertoires dans Azure Active Directory, ou comment ajouter des utilisateurs d’entreprises partenaires. Pour en savoir plus sur l’ajout de nouveaux utilisateurs dans votre organisation, et en particulier l’ajout d’utilisateurs disposant de comptes Microsoft, voir [Ajout ou modification d’utilisateurs dans Azure Active Directory](active-directory-create-users.md). Par défaut, les utilisateurs ajoutés ne reçoivent pas d’autorisations d’administrateur, mais vous pouvez leur attribuer des rôles à tout moment.

## <a name="add-a-user"></a>Ajouter un utilisateur
1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Active Directory**, puis ouvrez votre répertoire.
3. Sélectionnez l’onglet **Utilisateurs** et, dans la barre de commandes, sélectionnez **Ajouter un utilisateur**.
4. Sur la page **Dites-nous en plus sur cet utilisateur**, sous **Type d’utilisateur**, sélectionnez l’une des options suivantes :

   * **Utilisateur dans un autre annuaire Azure AD** : permet d’ajouter un compte d’utilisateur à votre répertoire, en provenance d’un autre répertoire Azure AD. Vous pouvez sélectionner un utilisateur dans un autre répertoire uniquement si vous êtes également membre de ce répertoire.
   * **Utilisateurs dans les entreprises partenaires** : permet d’inviter et d’autoriser les utilisateurs des entreprises partenaires dans votre répertoire (voir [Collaboration B2B Azure Active Directory](active-directory-b2b-what-is-azure-ad-b2b.md)). Vous devrez [télécharger un fichier CSV spécifiant des adresses e-mail](active-directory-b2b-references-csv-file-format.md).
5. Sur la page **Profil** de l’utilisateur, entrez les nom et prénom de l’utilisateur, un nom convivial et un rôle d’utilisateur, à partir de la liste **Rôles**. Pour plus d’informations sur les utilisateurs et les rôles d’administrateur, consultez la page [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md). Indiquez si vous voulez choisir l’option **Activer l’authentification multifacteur** pour cet utilisateur.
6. Sur la page **Obtenir un mot de passe temporaire**, sélectionnez **Créer**.

> [!IMPORTANT]
> Si votre organisation utilise plusieurs domaines, vous devez avoir connaissance des problèmes suivants lorsque vous ajoutez un compte d’utilisateur :
>
> * Pour ajouter des comptes d’utilisateurs avec le même nom d’utilisateur principal (UPN) sur plusieurs domaines, ajoutez **d’abord**, par exemple, geoffgrisso@contoso.onmicrosoft.com, **suivi de** geoffgrisso@contoso.com.
> * **N’ajoutez pas** geoffgrisso@contoso.com avant d’avoir ajouté geoffgrisso@contoso.onmicrosoft.com. Il est important de respecter cet ordre et il peut être difficile de revenir en arrière.
>
>

Si vous modifiez les informations d’un utilisateur dont l’identité est synchronisée avec votre service Active Directory local, vous ne pouvez pas modifier les informations utilisateur dans le portail Azure Classic. Pour modifier les informations d’un utilisateur, servez-vous de vos outils de gestion Active Directory locaux.

## <a name="add-external-users"></a>Ajouter des utilisateurs externes
Vous pouvez également ajouter des utilisateurs à partir d’un autre répertoire Azure AD auquel vous appartenez ou à partir d’entreprises partenaires en téléchargeant un fichier CSV. Pour ajouter un utilisateur externe, indiquez **Utilisateur dans un autre annuaire Microsoft Azure AD** ou **Utilisateurs dans les entreprises partenaires** pour **Type d’utilisateur**.

Les deux types d’utilisateurs proviennent d’un autre répertoire et sont ajoutés en tant **qu’utilisateurs externes**. Les utilisateurs externes peuvent collaborer avec d’autres utilisateurs dans un répertoire sans qu’il y ait besoin d’ajouter de nouveaux comptes et informations d’identification. Quand ils se connectent, les utilisateurs externes sont authentifiés par leur répertoire de base, et cette authentification fonctionne pour tout autre répertoire dans lequel ils ont été ajoutés.

## <a name="external-user-management-and-limitations"></a>Gestion des utilisateurs externes et limitations
Lorsque vous ajoutez à votre annuaire un utilisateur provenant d’un autre annuaire, cet utilisateur est ajouté à votre annuaire en tant qu’utilisateur externe. Le nom d’affichage et le nom d’utilisateur sont copiés à partir de son répertoire de base et utilisés pour l’utilisateur externe dans votre répertoire. Dès lors, les propriétés du compte d’utilisateur externe sont entièrement indépendantes. Si des modifications sont apportées aux propriétés de l’utilisateur dans son répertoire de base, ces modifications ne seront pas appliquées au compte d’utilisateur externe dans votre répertoire.

Le seul lien entre les deux comptes est que l’utilisateur s’authentifie toujours auprès de l’annuaire de base ou à l’aide de son compte Microsoft. C’est pourquoi l’option de réinitialisation du mot de passe ou de l’activation de l’authentification multifacteur ne s’affiche pas pour un utilisateur externe. Actuellement, la stratégie d’authentification du répertoire de base ou du compte Microsoft est la seule qui est évaluée lorsque l’utilisateur se connecte.

> [!NOTE]
> Vous pouvez quand même désactiver l’utilisateur externe dans le répertoire, ce qui l’empêche d’accéder à votre répertoire.
>
>

Si un utilisateur est supprimé de son annuaire de base ou s’il annule son compte Microsoft, l’utilisateur externe existe toujours dans votre annuaire. Cependant, l’utilisateur de votre répertoire ne peut pas accéder aux ressources étant donné qu’il ne peut pas s’authentifier avec son répertoire de base ni à l’aide de son compte Microsoft.

### <a name="services-that-currently-support-access-by-azure-ad-external-users"></a>Services auxquels peuvent avoir accès les utilisateurs externes Azure AD
* **Portail Azure Classic**: permet aux utilisateurs externes administrant plusieurs répertoires de gérer chacun d’entre eux.
* **SharePoint Online**: si le partage externe est activé, permet aux utilisateurs externes d’accéder aux ressources SharePoint Online autorisées.
* **Dynamics CRM**: permet aux utilisateurs externes d’accéder aux ressources Dynamics CRM autorisées, s’ils sont sous licence via PowerShell.
* **Dynamics AX**: permet aux utilisateurs externes d’accéder aux ressources Dynamics AX autorisées s’ils sont sous licence via PowerShell. Les limitations relatives aux [utilisateurs externes Azure AD](#known-limitations-of-azure-ad-external-users) s’appliquent également aux utilisateurs externes dans Dynamics AX.

### <a name="known-limitations-of-azure-ad-external-users"></a>Limitations connues des utilisateurs externes d’Azure AD
* Les utilisateurs externes qui sont administrateurs ne peuvent pas ajouter d’utilisateurs issus d’entreprises partenaires à des répertoires (collaboration B2B) en dehors de leur répertoire de base
* Les utilisateurs externes ne peuvent pas donner leur consentement aux applications mutualisées dans des répertoires en dehors de leur répertoire de base
* Pour l’instant, les utilisateurs externes ne peuvent pas accéder à PowerBI
* Les utilisateurs externes ne peuvent pas avoir de licences pour le portail Office
* En ce qui concerne Azure AD PowerShell, les utilisateurs externes sont connectés à leur annuaire de base et ne peuvent pas gérer les annuaires dans lesquels ils sont utilisateurs externes
* Lorsque vous configurez le plug-in Outlook de Dynamics CRM, les utilisateurs externes peuvent devoir entrer manuellement l’URL du client Dynamics CRM Online au lieu de sélectionner « CRM Online »

## <a name="whats-next"></a>Étapes suivantes
* [Ajout de nouveaux utilisateurs à Azure Active Directory](active-directory-create-users.md)
* [Administration d’Azure AD](active-directory-administer.md)
* [Gestion des mots de passe dans Azure AD](active-directory-manage-passwords.md)
* [Gestion des groupes dans Azure AD](active-directory-manage-groups.md)



<!--HONumber=Jan17_HO5-->


