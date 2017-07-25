---
title: "Problème de connexion sur le site web du volet d’accès | Microsoft Docs"
description: "Conseils pour résoudre les problèmes que vous pouvez rencontrer lors de la connexion pour utiliser le volet d’accès"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: bbd57547a0cc486a9cf7c8030dccaaf6f46c9860
ms.contentlocale: fr-fr
ms.lasthandoff: 04/11/2017

---

<a id="problem-signing-in-to-the-access-panel-website" class="xliff"></a>

# Problème de connexion sur le site web du volet d’accès

Le volet d’accès est un portail Web qui permet à un utilisateur disposant d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD) d’afficher et de lancer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. Un utilisateur disposant d’éditions Azure AD peut également utiliser des fonctionnalités de gestion de groupes et d’applications en libre-service via le volet d’accès. Le volet d’accès est distinct du portail Azure et n’exige pas des utilisateurs qu’ils aient un abonnement Azure.

Les utilisateurs peuvent se connecter au volet d’accès s’ils possèdent un compte professionnel ou scolaire dans Azure AD.

-   Les utilisateurs peuvent être authentifiés par Azure AD directement.

-   Les utilisateurs peuvent être authentifiés à l’aide d’Active Directory Federation Services (ADFS).

-   Les utilisateurs peuvent être authentifiés par Windows Server Active Directory.

Si un utilisateur dispose d’un abonnement Azure ou Office 365 et s’il utilise le portail Azure ou une application Office 365, il pourra utiliser le volet d’accès de façon transparente sans devoir se connecter à nouveau. Les utilisateurs qui ne sont pas authentifiés sont invités à se connecter à l’aide du nom d’utilisateur et du mot de passe correspondant à leur compte dans Azure AD. Si l’organisation a configuré la fédération, la saisie du nom d’utilisateur suffit.

<a id="general-issues-to-check-first" class="xliff"></a>

## Problèmes d’ordre général à vérifier en premier 

-   Assurez-vous que l’utilisateur se connecte à **l’URL correcte** : <https://myapps.microsoft.com>

-   Vérifiez que l’URL figure dans la liste des **sites de confiance** du navigateur de l’utilisateur.

-   Vérifiez que les connexions sont **activées** dans le compte de l’utilisateur.

-   Vérifiez que le compte d’utilisateur **n’est pas verrouillé**.

-   Vérifiez que le **mot de passe de l’utilisateur n’a pas expiré ou qu’il n’a pas été oublié**.

-   Vérifiez que **Multi-Factor Authentication** ne bloque pas l’accès utilisateur.

-   Vérifiez qu’une **stratégie d’accès conditionnel** ou **stratégie de protection d’identité** ne bloque pas l’accès utilisateur.

-   Vérifiez que les **informations de contact d’authentification** de l’utilisateur sont à jour et permettent l’application des stratégies d’accès conditionnel ou de Multi-Factor Authentication.

-   Essayez également d’effacer les cookies de votre navigateur, puis réessayez de vous connecter.

<a id="meeting-browser-requirements-for-the-access-panel" class="xliff"></a>

## Configuration requise du navigateur pour le volet d’accès

Le volet d’accès nécessite un navigateur qui prend en charge JavaScript et dans lequel le CSS est activé. Pour utiliser l’authentification unique basée sur un mot de passe dans le volet d’accès, l’extension du volet d’accès doit être installée dans le navigateur de l’utilisateur. Cette extension est téléchargée automatiquement lorsqu’un utilisateur sélectionne une application configurée pour l’authentification unique (SSO) avec mot de passe.

Pour l’authentification unique par mot de passe, les navigateurs de l’utilisateur final peuvent être :

-   Internet Explorer 8, 9, 10, 11 -- sur Windows 7 ou version ultérieure

-   Chrome -- sur Windows 7 ou ultérieur, et sur Mac OS X ou ultérieur

-   Firefox 26.0 ou ultérieur -- sur Windows XP SP2 ou ultérieur, et sur Mac OS X 10.6 ou ultérieur

>[!NOTE] 
>L’extension de l’authentification unique basée sur un mot de passe sera disponible pour Edge dans Windows 10 quand les extensions de navigateur seront prises en charge pour Edge.
>
>

<a id="problems-with-the-users-account" class="xliff"></a>

## Problèmes avec le compte de l’utilisateur

L’accès au volet d’accès peut être bloqué en raison d’un problème avec le compte de l’utilisateur. Voici quelques méthodes pour vous aider à résoudre les problèmes avec les utilisateurs et leurs paramètres de compte :

-   [Vérifier l’existence d’un compte d’utilisateur dans Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Vérifier l’état du compte d’un utilisateur](#check-a-users-account-status)

-   [Réinitialiser le mot de passe d’un utilisateur](#reset-a-users-password)

-   [Activer la réinitialisation du mot de passe libre-service](#enable-self-service-password-reset)

-   [Vérifier l’état Multi-Factor Authentication d’un utilisateur](#check-a-users-multi-factor-authentication-status)

-   [Vérifier les informations de contact de l’authentification d’un utilisateur](#check-a-users-authentication-contact-info)

-   [Vérifier les appartenances d’un utilisateur à des groupes](#check-a-users-group-memberships)

-   [Vérifier les licences affectées à un utilisateur](#check-a-users-assigned-licenses)

-   [Affecter une licence à un utilisateur](#assign-a-user-a-license)

<a id="check-if-a-user-account-exists-in-azure-active-directory" class="xliff"></a>

### Vérifier l’existence d’un compte d’utilisateur dans Azure Active Directory

Pour vérifier si le compte d’un utilisateur est présent, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Plus de services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** à sélectionner.

7.  Vérifiez les propriétés de l’objet utilisateur pour vous assurer qu’elles apparaissent comme prévu et qu’aucune donnée n’est manquante.

<a id="check-a-users-account-status" class="xliff"></a>

### Vérifier l’état du compte d’un utilisateur

Pour vérifier l’état du compte d’un utilisateur, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Plus de services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** à sélectionner.

7.  Cliquez sur **Profil**.

8.  Sous **Paramètres**, assurez-vous que **Bloquer la connexion** est défini sur **Non**.

<a id="reset-a-users-password" class="xliff"></a>

### Réinitialiser le mot de passe d’un utilisateur

Pour réinitialiser le mot de passe d’un utilisateur, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Plus de services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** à sélectionner.

7.  Cliquez sur le bouton **Réinitialiser le mot de passe** en haut du panneau Utilisateur.

8.  Cliquez sur le bouton **Réinitialiser le mot de passe** sur le panneau **Réinitialiser le mot de passe** qui s’affiche.

9.  Copiez le **mot de passe temporaire** ou **entrez un nouveau mot de passe** pour l’utilisateur.

10. Communiquez ce nouveau mot de passe à l’utilisateur ; ce dernier devra modifier ce mot de passe lors de sa prochaine connexion à Azure Active Directory.

<a id="enable-self-service-password-reset" class="xliff"></a>

### Activer la réinitialisation du mot de passe libre-service

Pour activer la réinitialisation du mot de passe libre-service, suivez les étapes de déploiement ci-dessous :

-   [Permettre aux utilisateurs de réinitialiser leur mot de passe Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Permettre aux utilisateurs de réinitialiser ou de modifier leur mot de passe Active Directory Azure local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

<a id="check-a-users-multi-factor-authentication-status" class="xliff"></a>

### Vérifier l’état Multi-Factor Authentication d’un utilisateur

Pour vérifier l’état Multi-Factor Authentication d’un utilisateur, suivez les étapes ci-dessous :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Plus de services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  Cliquez sur le bouton **Multi-Factor Authentication** en haut du panneau.

7.  Une fois le **portail d’administration Multi-Factor Authentication** chargé, assurez-vous de vous trouver sur l’onglet **Utilisateurs**.

8.  Recherchez l’utilisateur dans la liste des utilisateurs au moyen de la recherche, du filtrage ou du tri.

9.  Sélectionnez l’utilisateur dans la liste des utilisateurs et **activez**, **désactivez** ou **appliquez** la Multi-Factor Authentication comme souhaité.

   >[!NOTE]
   >Si un utilisateur est dans un état **Appliqué**, vous pouvez définir le définir sur **Désactivé** de façon temporaire pour lui permettre de revenir à son compte. Une fois qu’il est revenu dans son compte, vous pouvez ensuite modifier son état en le redéfinissant sur **Activé** pour lui demander d’enregistrer à nouveau ses informations de contact à sa prochaine connexion. Sinon, vous pouvez suivre les étapes décrites dans [Vérifier les informations de contact de l’authentification d’un utilisateur](#check-a-users-authentication-contact-info) pour vérifier ou définir ces données à sa place.
   >
   >

<a id="check-a-users-authentication-contact-info" class="xliff"></a>

### Vérifier les informations de contact de l’authentification d’un utilisateur

Pour vérifier les informations de contact de l’authentification d’un utilisateur utilisées pour Multi-Factor Authentication, Accès conditionnel, Identity Protection et Réinitialisation de mot de passe, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Plus de services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** à sélectionner.

7.  Cliquez sur **Profil**.

8.  Faites défiler jusqu’à **Informations de contact d’authentification**.

9.  **Passez en revue** les données enregistrées pour l’utilisateur et mettez-les à jour si besoin.

<a id="check-a-users-group-memberships" class="xliff"></a>

### Vérifier les appartenances d’un utilisateur à des groupes

Pour vérifier l’appartenance d’un utilisateur à des groupes, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Plus de services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** à sélectionner.

7.  Cliquez sur **Groupes** pour afficher les groupes dont l’utilisateur est membre.

<a id="check-a-users-assigned-licenses" class="xliff"></a>

### Vérifier les licences affectées à un utilisateur

Pour vérifier les licences affectées à un utilisateur, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Plus de services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** à sélectionner.

7.  Cliquez sur **Licences** pour voir quelles licences sont actuellement affectées à l’utilisateur.

<a id="assign-a-user-a-license" class="xliff"></a>

### Affecter une licence à un utilisateur 

Pour affecter une licence à un utilisateur, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Plus de services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** à sélectionner.

7.  Cliquez sur **Licences** pour voir quelles licences sont actuellement affectées à l’utilisateur.

8.  Cliquez sur le bouton **Attribuer**.

9.  Sélectionnez **un ou plusieurs produits** à partir de la liste des produits disponibles.

10. **Facultatif** Cliquez sur l’élément **Options d’affectation** pour affecter les produits de façon granulaire. Cliquez sur **Ok** lorsque l’opération est terminée.

11. Cliquez sur le bouton **Attribuer** pour affecter ces licences à cet utilisateur.

<a id="if-these-troubleshooting-steps-do-not-resolve-the-issue" class="xliff"></a>

## Si ces étapes de dépannage ne résolvent pas le problème

Créez un ticket de support en fournissant les informations suivantes, si disponibles :

-   ID d’erreur de corrélation

-   Nom d’utilisateur principal (adresse de messagerie de l’utilisateur)

-   ID client

-   Type de navigateur

-   Fuseau horaire et heure/période à laquelle l’erreur s’est produite

-   Traces Fiddler

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)

