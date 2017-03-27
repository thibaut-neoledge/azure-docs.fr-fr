---
title: Forum aux questions Azure Active Directory B2B Collaboration | Microsoft Docs
description: Forum aux questions sur Azure Active Directory B2B Collaboration
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
ms.date: 03/14/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 4b7ed095729e810f7f1112d3b6becfaf186bf508
ms.lasthandoff: 03/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Forum aux questions d'Azure Active Directory B2B Collaboration

Le forum aux questions est régulièrement mis à jour pour refléter les nouveaux centres d’intérêt.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>Cette fonction est-elle disponibles dans le portail Azure Classic ?
Les nouvelles fonctionnalités de cette actualisation de la version préliminaire publique d'Azure AD B2B Collaboration sont uniquement disponibles par le biais du [portail Azure](https://portal.azure.com) et le nouveau panneau d’accès. Essayez !

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Les utilisateurs de B2B Collaboration peuvent-ils accéder à SharePoint Online et OneDrive ?
Les utilisateurs invités de B2B Collaboration figurent dans le répertoire. Vous pouvez les ajouter aux groupes auxquels vous donnez une autorisation d'accès aux sites SharePoint Online et OneDrive, ou même les sélectionner directement dans le sélecteur de personnes SharePoint Online. Comme il s’agit d’utilisateurs invités, le partage externe doit être activé sur les sites SharePoint Online.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>Le mécanisme de chargement CSV est-il toujours pris en charge ?
Oui. Reportez-vous à l’exemple PowerShell que nous avons inclus.

### <a name="how-can-i-customize-my-invitation-emails"></a>Comment puis-je personnaliser mes e-mails d’invitation ?
Vous pouvez personnaliser pratiquement tous les éléments du processus de l’inviteur à l’aide d'API d’invitation B2B.

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>L’utilisateur externe invité peut-il quitter l’organisation à laquelle il a été invité ?
Ce n'est pas possible actuellement dans l’actualisation de cette version préliminaire publique.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>Maintenant que l’authentification multifacteur (Multi-Factor Authentication, MFA) est disponible pour les utilisateurs invités, peuvent-ils également réinitialiser leur méthode d’authentification MFA ?
Oui, la même façon que les utilisateurs standard.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>Quelle organisation est responsable des licences MFA ?
L’organisation qui invite est l’organisation qui intervient et exécute l’authentification MFA. Par conséquent, l’organisation qui invite doit s’assurer de disposer de suffisamment de licences pour les utilisateurs B2B exécutant l’authentification MFA.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>Que se passe-t-il si mon organisation partenaire a déjà configuré l’authentification MFA ? Pouvons-nous faire confiance leur authentification MFA et ne pas utiliser la nôtre ?
Pas dans cette actualisation de la version préliminaire publique, mais nous prévoyons une prise en charge dans des futures versions. Quand elle sera possible, vous pourrez sélectionner des partenaires spécifiques à exclure de votre authentification MFA (de l'organisation qui invite).

### <a name="how-can-i-achieve-delayed-invitations"></a>Comment puis-je créer des invitations différées ?
Certaines organisations souhaitent ajouter des utilisateurs B2B Collaboration et les approvisionner pour des applications qui nécessitent un approvisionnement avant d'envoyer des invitations. Si c’est vous, vous pouvez utiliser l’API d'invitation de B2B Collaboration pour personnaliser le workflow d’intégration.

### <a name="can-guest-users-and-contacts-co-exist"></a>Les utilisateurs invités et les contacts peuvent-ils coexister ?
Votre organisation peut avoir ajouté des contacts représentant des collaborateurs externes afin qu’ils apparaissent dans la liste d’adresses globale et en tant que suggestions d’adresse e-mail lors de la rédaction d'un e-mail. Vous vous demandez peut-être ce qui se passe lorsque vous ajoutez ces mêmes collaborateurs en tant qu’utilisateurs de B2B Collaboration dans le répertoire ? Dans une version ultérieure, les utilisateurs de B2B Collaboration et vos objets de contact seront en mesure de coexister dans le répertoire de votre entreprise. Ne manquez pas nos prochaines annonces !

### <a name="can-i-make-my-guest-users-limited-admins"></a>Puis-je faire de mes utilisateurs invités des administrateurs limités ?
Absolument. Si vous en avez besoin pour votre organisation, découvrez comment [ajouter des utilisateurs invités à un rôle](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B Collaboration prend-il en charge l’octroi aux utilisateurs B2B d'un accès au portail Azure ?
Les utilisateurs de B2B Collaboration n'ont pas besoin d'accéder au portail Azure, sauf si un rôle d'administrateur limité ou d'administrateur général leur est affecté. Dans ce cas, ils peuvent accéder au portail. Si un utilisateur n'ayant pas l'un de ces rôles accède au portail, il/elle peut être en mesure d’accéder à certaines parties de l’expérience, car le rôle d’utilisateur invité comprend certaines autorisations dans le répertoire, comme décrit dans les sections précédentes.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Puis-je bloquer l’accès au portail Azure pour les utilisateurs invités ?
Oui. Mais soyez prudent lorsque vous configurez cette stratégie afin d'éviter de bloquer accidentellement l’accès pour les membres et les administrateurs.
Vous pouvez bloquer l’accès au [portail Azure](https://portal.azure.com) par les utilisateurs invités à l'aide d'une stratégie d’accès conditionnel sur des API Gestion des services Windows Azure en suivant les trois étapes suivantes.
1. Modifiez le groupe **Tous les utilisateurs** de manière à ce qu'il ne contienne que des membres ![](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Créez un groupe dynamique contenant des utilisateurs invités ![](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configurez une stratégie d’accès conditionnel pour empêcher les utilisateurs invités d’accéder au portail, comme indiqué dans la vidéo suivante.

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player]

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Quand la prise en charge de l’authentification MFA et des comptes de messagerie de consommateurs par Azure AD B2B Collaboration démarrera-t-elle ?
L’authentification MFA et les comptes de messagerie de consommateurs sont désormais pris en charge dans cette actualisation de la version préliminaire publique.

### <a name="what-is-the-ga-timeline-for-azure-ad-b2b"></a>Quel est le délai de disponibilité générale d’Azure AD B2B ?
Ce délai dépendra des commentaires des clients sur l’ensemble de fonctionnalités actuel.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Existe-t-il un plan pour la prise en charge de la réinitialisation de mot de passe pour les utilisateurs Azure AD B2B Collaboration ?
Oui, une prise en charge existe pour les utilisateurs (invités) de B2B Collaboration.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>Est-elle également activée pour les utilisateurs dans un locataire viral ?
Pas actuellement.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM fournit-il un support en ligne pour Azure AD B2B Collaboration ?
CRM fournira un support pour Azure AD B2B Collaboration après la mise à disposition générale.

### <a name="are-b2b-collaboration-guest-users-visible-in-sharepoint-onlineonedrive-people-picker"></a>Les utilisateurs invités Collaboration B2B sont-ils visibles dans le sélecteur de personnes SharePoint Online/OneDrive ?
 
Oui. La fonctionnalité de recherche d’utilisateurs invités existants dans le sélecteur de personnes SharePoint Online est cependant désactivée par défaut pour correspondre au comportement hérité. Vous pouvez l’activer à l’aide du paramètre ShowPeoplePickerSuggestionsForGuestUsers au niveau du client et de la collection du site. Elle peut être définie à l’aide des applets de commande Set-SPOTenant et Set-SPOSite qui permettent aux membres de rechercher tous les utilisateurs invités existants dans le répertoire. Les modifications apportées à la portée du client n’affectent pas les sites SharePoint Online déjà configurés.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Quelle est la durée de vie d’un mot de passe initial pour un utilisateur B2B Collaboration nouvellement créé ?
Azure AD dispose d’un ensemble fixe d'exigences en matière de nombre de caractères, de force du mot de passe et de verrouillage de compte qui s’appliquent à tous les comptes d’utilisateur cloud Azure AD. Les comptes d’utilisateur cloud sont les comptes qui ne sont pas fédérés avec un autre fournisseur d’identité tel que Microsoft Account, Facebook, ADFS ou même un autre locataire cloud (dans le cas de B2B Collaboration). Pour les comptes fédérés, la stratégie de mot de passe dépend de la stratégie dans la location locale et des paramètres de compte Microsoft de l’utilisateur.

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>Les applications cherchent à différencier leurs expériences entre un utilisateur client et un utilisateur invité. Une assistance standard est-elle disponible pour cela ? La présence de la revendication de fournisseur d’identité est-elle le modèle approprié ?
 
Un utilisateur invité peut utiliser n’importe quel fournisseur d’identité pour s’authentifier comme décrit dans [Propriétés d’un utilisateur Collaboration B2B](active-directory-b2b-user-properties.md). La propriété UserType est donc la propriété appropriée pour le déterminer. La revendication UserType n’est actuellement pas incluse dans le jeton. Les applications doivent utiliser l’API Graph pour interroger le répertoire de l’utilisateur et obtenir leur UserType.

### <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [API et personnalisation d’Azure Active Directory B2B Collaboration](active-directory-b2b-api.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

