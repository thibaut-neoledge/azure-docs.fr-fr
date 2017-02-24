---
title: "Résolution des problèmes d’Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Solutions pour les problèmes courants liés à Azure Active Directory B2B Collaboration"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: f85c6bcc2abbd14c7879462f7013a97f550fdca5


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Résolution des problèmes d’Azure Active Directory B2B Collaboration

Voici des solutions pour les problèmes courants liés à Azure Active Directory (Azure AD) B2B Collaboration.

## <a name="i-cant-create-an-external-user-due-to-an-existing-contact"></a>Impossible de créer un utilisateur externe en raison d’un contact existant

Si l’utilisateur externe que vous invitez a déjà un objet de contact existant, vous ne pourrez pas inviter cet utilisateur tant que le conflit ne sera pas résolu, généralement en supprimant l’objet de contact. Le conflit doit être résolu manuellement jusqu'à la disponibilité générale de B2B Collaboration.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>J’ai ajouté un utilisateur externe, mais je ne le vois pas dans mon carnet d’adresses global ou dans le sélecteur de personnes

Dans les cas où les utilisateurs externes ne sont pas renseignés dans la liste, la réplication de l’objet peut prendre quelques minutes.

## <a name="invitations-have-been-disabled-for-directory"></a>Des invitations ont été désactivées pour le répertoire

Si vous recevez un message d’erreur indiquant que vous n'êtes pas autorisé à inviter des utilisateurs, vérifiez que votre compte d’utilisateur est autorisé à inviter des utilisateurs externes. Pour cela, accédez aux Paramètres utilisateur :

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Si vous avez récemment modifié ces paramètres ou affecté le rôle d’inviteur d’invités à un utilisateur, vous devrez peut-être attendre 15 à 60 minutes avant que les modifications ne prennent effet.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>L’utilisateur que j’ai invité reçoit une erreur au cours de l’utilisation de l'invitation

Les erreurs courantes sont les suivantes :

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>L'administrateur de l’inviteur n’autorise pas la création d'utilisateurs EmailVerified dans leur locataire :

Si vous invitez des utilisateurs dont l’organisation utilise un Azure Active Directory dans lequel le compte d’utilisateur spécifique n’existe pas (l’utilisateur n’existe pas dans AAD contoso.com). L’administrateur de contoso.com peut avoir mis en place une stratégie empêchant la création d'utilisateurs. Dans le cas où les utilisateurs externes sont autorisés, l’utilisateur externe doit vérifier auprès de son administrateur si l'administrateur de l’utilisateur externe devra peut-être autoriser les utilisateurs d'e-mails vérifiés dans le domaine (voir cet [article](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msolcompanysettings#parameters) sur l'autorisation d'utilisateurs EmailVerified).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>L'utilisateur externe n’existe pas déjà dans un domaine fédéré

Dans les cas où l’utilisateur externe utilise une solution de fédération où l’authentification est effectuée localement et l’utilisateur n’existe pas déjà dans Azure Active Directory, l’utilisateur ne peut pas être invité.

Pour résoudre ce problème, administrateur de l’utilisateur externe doit synchroniser le compte d’utilisateur sur Azure Active Directory.

## <a name="how-does--which-is-normally-an-invalid-character-sync-with-azure-ad"></a>Comment synchroniser « \# », qui est normalement un caractère non valide, avec Azure AD ?

« \# » est un caractère réservé dans les UPN pour Azure AD B2B Collaboration ou des utilisateurs externes (autrement dit, &lt;user@contoso.com&gt; devient &lt;user_contoso.com#EXT@fabrikam.onmicrosoft.com&gt; une fois invité), donc \# dans des UPN locaux ne sont pas autorisés pour la connexion au portail Azure.

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Je reçois une erreur lors de l’ajout d'utilisateurs externes à un groupe synchronisé

Des utilisateurs externes peuvent être ajoutés uniquement à des groupes « affectés » ou « de sécurité » et non à des groupes contrôlés localement.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mon utilisateur externe n’a pas reçu d'e-mail à utiliser

L’invité doit contacter son fournisseur de services Internet ou contrôler son filtre de courriers indésirables pour s’assurer que l’adresse suivante est autorisée :&lt;Invites@microsoft.com&gt;

## <a name="my-recipient-received-multiple-emails-from-me"></a>Mon destinataire a reçu plusieurs e-mails de ma part

Dans certains cas, lorsque le destinataire de l’invitation a plusieurs alias pour son compte, il se peut qu'il reçoive deux invitations. Dans ces cas, le premier lien utilisé est le compte qui sera créé, et le second lien d'utilisation de l'invitation n'est alors plus valide.

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [API et personnalisation d’Azure Active Directory B2B Collaboration](active-directory-b2b-api.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


