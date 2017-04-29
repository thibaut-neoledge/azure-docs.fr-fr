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
ms.date: 04/13/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: c06e8b98fea6036704b07b0f12397fc4bb59cb3f
ms.lasthandoff: 04/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Forum aux questions d'Azure Active Directory B2B Collaboration

Le forum aux questions est régulièrement mis à jour pour refléter les nouveaux centres d’intérêt.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>Cette fonction est-elle disponibles dans le portail Azure Classic ?
Les nouvelles fonctionnalités d’Azure AD B2B Collaboration sont uniquement disponibles par le biais du [portail Azure](https://portal.azure.com) et le nouveau panneau d’accès. 

### <a name="is-it-possible-to-customize-our-sign-in-page-so-its-more-intuitive-for-your-b2b-collaboration-guest-users"></a>Est-il possible de personnaliser notre page de connexion afin qu’elle soit plus intuitive pour vos utilisateurs invités B2B Collaboration ?
Absolument ! Il existe un [billet de blog expliquant la fonctionnalité](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/) et il est présent dans [Ajout d’une marque de société aux pages de connexion et du volet d’accès](active-directory-add-company-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Les utilisateurs de B2B Collaboration peuvent-ils accéder à SharePoint Online et OneDrive ?
Oui. La fonctionnalité de recherche d’utilisateurs invités existants dans le sélecteur de personnes SharePoint Online est cependant désactivée par défaut pour correspondre au comportement hérité. Vous pouvez l’activer à l’aide du paramètre ShowPeoplePickerSuggestionsForGuestUsers au niveau du client et de la collection du site. Elle peut être définie à l’aide des applets de commande Set-SPOTenant et Set-SPOSite qui permettent aux membres de rechercher tous les utilisateurs invités existants dans le répertoire. Les modifications apportées à la portée du client n’affectent pas les sites SharePoint Online déjà configurés.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>Le mécanisme de chargement CSV est-il toujours pris en charge ?
Oui. Reportez-vous à l’[exemple PowerShell](active-directory-b2b-code-samples.md) que nous avons inclus.

### <a name="how-can-i-customize-my-invitation-emails"></a>Comment puis-je personnaliser mes e-mails d’invitation ?
Vous pouvez personnaliser pratiquement tous les éléments du processus de l’inviteur à l’aide des [API d’invitation B2B](active-directory-b2b-api.md).

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>L’utilisateur externe invité peut-il quitter l’organisation à laquelle il a été invité ?
Ceci n’est actuellement pas possible.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>Maintenant que l’authentification multifacteur (Multi-Factor Authentication, MFA) est disponible pour les utilisateurs invités, peuvent-ils également réinitialiser leur méthode d’authentification MFA ?
Oui, la même façon que les utilisateurs standard.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>Quelle organisation est responsable des licences MFA ?
L’organisation qui invite est l’organisation qui intervient et exécute l’authentification MFA. Par conséquent, l’organisation qui invite doit s’assurer de disposer de suffisamment de licences pour les utilisateurs B2B exécutant l’authentification MFA.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>Que se passe-t-il si mon organisation partenaire a déjà configuré l’authentification MFA ? Pouvons-nous faire confiance leur authentification MFA et ne pas utiliser la nôtre ?
Nous prévoyons une prise en charge dans les versions à venir. Quand elle sera possible, vous pourrez sélectionner des partenaires spécifiques à exclure de votre authentification MFA (de l'organisation qui invite).

### <a name="how-can-i-achieve-delayed-invitations"></a>Comment puis-je créer des invitations différées ?
Certaines organisations souhaitent ajouter des utilisateurs B2B Collaboration et les approvisionner pour des applications qui nécessitent un approvisionnement avant d'envoyer des invitations. Si c’est vous, vous pouvez utiliser l’API d'invitation de B2B Collaboration pour personnaliser le workflow d’intégration.

### <a name="can-i-make-my-guest-users-limited-admins"></a>Puis-je faire de mes utilisateurs invités des administrateurs limités ?
Absolument. Si vous en avez besoin pour votre organisation, découvrez comment [ajouter des utilisateurs invités à un rôle](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B Collaboration prend-il en charge l’octroi aux utilisateurs B2B d'un accès au portail Azure ?
Les utilisateurs de B2B Collaboration n'ont pas besoin d'accéder au portail Azure, sauf si un rôle d'administrateur limité ou d'administrateur général leur est affecté. Dans ce cas, ils peuvent accéder au portail. Si un utilisateur n'ayant pas l'un de ces rôles accède au portail, il/elle peut être en mesure d’accéder à certaines parties de l’expérience, car le rôle d’utilisateur invité comprend certaines autorisations dans le répertoire, comme décrit dans les sections précédentes.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Puis-je bloquer l’accès au portail Azure pour les utilisateurs invités ?
Oui. Mais soyez prudent lorsque vous configurez cette stratégie afin d'éviter de bloquer accidentellement l’accès pour les membres et les administrateurs.
Vous pouvez bloquer l’accès au [portail Azure](https://portal.azure.com) par les utilisateurs invités à l'aide d'une stratégie d’accès conditionnel sur des API Gestion des services Windows Azure en suivant les trois étapes suivantes.
1. Modifiez le groupe **Tous les utilisateurs** de manière à ce qu’il ne contienne que des membres ![modifiez la capture d’écran de groupe](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Créez un groupe dynamique contenant des utilisateurs invités ![créez une capture d’écran de groupe](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configurez une stratégie d’accès conditionnel pour empêcher les utilisateurs invités d’accéder au portail, comme indiqué dans la vidéo suivante.
  
  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Quand la prise en charge de l’authentification MFA et des comptes de messagerie de consommateurs par Azure AD B2B Collaboration démarrera-t-elle ?
L’authentification MFA et les comptes de messagerie de consommateurs sont désormais pris en charge.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Existe-t-il un plan pour la prise en charge de la réinitialisation de mot de passe pour les utilisateurs Azure AD B2B Collaboration ?
Oui. Voici les informations sur la réinitialisation du mot de passe libre-service (SSPR) pour un utilisateur B2B qui est invité à une location de ressource à partir de sa location d’identité :
 
* La réinitialisation du mot de passe libre-service ne s’effectuera que dans la location d’identité de l’utilisateur B2B
* Si la location d’identité est un compte Microsoft ou utilise le mécanisme SSPR du compte Microsoft
* Si la location d’identité est une location virale ou juste-à-temps, un courrier électronique de réinitialisation de mot de passe est envoyé
* Pour les autres, le processus SSPR standard est suivi pour les utilisateurs B2B, comme pour les membres la réinitialisation du mot de passe libre-service pour les utilisateurs B2B dans le contexte de la location de ressources est bloquée.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>Est-elle également activée pour les utilisateurs dans un locataire viral ?
Pas actuellement.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM fournit-il un support en ligne pour Azure AD B2B Collaboration ?
Nous travaillons actuellement à mettre en place cette prise en charge.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Quelle est la durée de vie d’un mot de passe initial pour un utilisateur B2B Collaboration nouvellement créé ?
Azure AD dispose d’un ensemble fixe d'exigences en matière de nombre de caractères, de force du mot de passe et de verrouillage de compte qui s’appliquent à tous les comptes d’utilisateur cloud Azure AD. Les comptes d’utilisateur cloud sont les comptes qui ne sont pas fédérés avec un autre fournisseur d’identité tel que Microsoft Account, Facebook, ADFS ou même un autre locataire cloud (dans le cas de B2B Collaboration). Pour les comptes fédérés, la stratégie de mot de passe dépend de la stratégie dans la location locale et des paramètres de compte Microsoft de l’utilisateur.

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>Les applications cherchent à différencier leurs expériences entre un utilisateur client et un utilisateur invité. Une assistance standard est-elle disponible pour cela ? La présence de la revendication de fournisseur d’identité est-elle le modèle approprié ?
 
Un utilisateur invité peut utiliser n’importe quel fournisseur d’identité pour s’authentifier comme décrit dans [Propriétés d’un utilisateur Collaboration B2B](active-directory-b2b-user-properties.md). La propriété UserType est donc la propriété appropriée pour le déterminer. La revendication UserType n’est actuellement pas incluse dans le jeton. Les applications doivent utiliser l’API Graph pour interroger le répertoire de l’utilisateur et obtenir leur UserType.

### <a name="where-can-find-a-b2b-collaboration-community-to-share-solutions-and-submit-ideas"></a>Où puis-je trouver une communauté B2B Collaboration pour partager des solutions et envoyer des idées ?

Nous sommes constamment à l’écoute de vos commentaires sur les moyens d’améliorer B2B Collaboration. Nous vous invitons à participer à la discussion, à partager vos scénarios utilisateur, les meilleures pratiques et ce que vous appréciez par rapport à Azure AD B2B Collaboration sur la page [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Nous vous invitons également à soumettre vos idées et à voter pour les prochaines fonctionnalités sur le site [Idées B2B Collaboration](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="is-there-a-way-to-invite-the-user-such-that-the-invitation-is-automatically-redeemed-and-the-user-is-just-ready-to-go-or-will-the-user-always-have-to-click-through-to-the-redemption-url"></a>Existe-t-il un moyen d’inviter l’utilisateur de telle façon que l’invitation est remboursée automatiquement et l’utilisateur est simplement « prêt », ou l’utilisateur devra-t-il toujours cliquer pour accéder à l’URL de remboursement ?
Les invitations envoyées par un utilisateur de l’organisation à l’origine de l’invitation, qui est également un membre de l’organisation invitée (organisation de l’utilisateur B2B) ne nécessitent pas de remboursement par l’utilisateur B2B.

Pour y parvenir, nous vous recommandons d’inviter un utilisateur à partir de l’organisation invitée dans l’organisation à l’origine de l’invitation. [Ajoutez cet utilisateur au rôle Inviteur d’invités dans l’organisation de ressources](active-directory-b2b-add-guest-to-role.md). Cet utilisateur peut inviter d’autres utilisateurs dans l’organisation invitée via l’interface utilisateur de connexion, les scripts PowerShell, ou les API sans que l’utilisateur B2B de cette organisation doive utiliser leur invitation.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Comment fonctionne la collaboration B2B lorsque le partenaire invité utilise la fédération pour ajouter sa propre authentification locale ?
Si le partenaire a un client Azure AD qui est fédéré à l’infrastructure d’authentification locale, alors l’authentification unique (SSO) locale s’effectue automatiquement. Si le partenaire n’a aucun client Azure AD, un compte Azure AD est créé pour l’utilisateur entrant. 

### <a name="i-didnt-think-azure-ad-b2b-would-accept-gmailcom-and-outlookcom-email-addresses-b2c-was-what-you-used-for-those"></a>Je ne pensais pas qu’Azure AD B2B accepterait les adresses de messagerie gmail.com et outlook.com. Il fallait alors utiliser B2C.
Nous supprimons les différences entre B2B et B2C concernant les identités prises en charge. L’identité utilisée n’est pas la meilleure référence pour faire son choix entre B2B et B2C. Reportez-vous à cet article pour vous aider à déterminer lequel utiliser : [Comparer B2B Collaboration et B2C dans Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Quelles applications et services prennent en charge les utilisateurs invités Azure B2B ?
Toutes les applications intégrées Azure Active Directory. 

### <a name="is-it-possible-to-force-mfa-for-b2b-guest-users-if-partners-have-no-mfa-enabled"></a>Est-il possible de forcer l’authentification multifacteur (MFA) pour les utilisateurs invités B2B, si les partenaires ne l’ont pas activée ?
Oui. Plus d’informations dans [Accès conditionnel pour les utilisateurs de B2B Collaboration](active-directory-b2b-mfa-instructions.md).

### <a name="within-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-any-plans-to-extend-this-to-azure-or-across-all-of-office-365"></a>Dans SharePoint, il est possible de définir une liste « allow (autoriser) » ou « deny (refuser) » pour les utilisateurs externes. Avez-vous pour projet d’étendre cela à Azure ou sur l’ensemble d’Office 365 ?
Oui. Azure AD B2B Collaboration prend en charge la fonctionnalité allowlist/denylist. 

### <a name="what-licenses-are-needed-for-azure-ad-b2b"></a>Quelles licences sont nécessaires pour Azure AD B2B ?
Veuillez vous reporter au [Guide d’attribution de licences pour Azure Active Directory B2B Collaboration](active-directory-b2b-licensing.md).

### <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
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

