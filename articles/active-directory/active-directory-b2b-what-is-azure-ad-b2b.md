---
title: "Qu’est-ce qu’Azure Active Directory B2B Collaboration (version préliminaire) ? | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: 5e55afe6eb88a558ea4eb147860ac5e2ebc97dbc


---

# <a name="what-is-azure-ad-b2b-collaboration-preview"></a>Qu’est-ce qu’Azure AD B2B Collaboration (version préliminaire) ?

Les fonctionnalités d’Azure AD B2B Collaboration permettent aux professionnels de l’informatique et de l’information de travailler en étroite collaboration avec leurs partenaires de n’importe quelle entreprise dans le monde. Ils peuvent leur donner accès à des documents, des ressources et des applications tout en conservant le contrôle de leurs données internes. Les développeurs peuvent utiliser les API entreprise à entreprise d’Azure AD pour écrire des applications qui rapprochent deux organisations de manière sécurisée, transparente et facilement exploitable pour les professionnels de l’information.

Les fonctionnalités d’Azure AD B2B Collaboration permettent aux organisations de toutes tailles et dans tous les secteurs, indépendamment de leurs exigences en matière de conformité et de gouvernance, de travailler facilement et en toute sécurité avec des collaborateurs situés partout dans le monde. C’est l’objectif de cette actualisation de la version préliminaire publique de B2B Collaboration.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Dans la version préliminaire actuelle, pour établir une relation avec une organisation, les professionnels de l’informatique et de l’information peuvent ajouter des utilisateurs (individuellement ou plusieurs à la fois) à partir d’une autre organisation à l’aide du portail ou de l’API du Gestionnaire d’invitations. Les administrateurs peuvent utiliser les nouvelles expériences du portail dans le portail Azure (https://portal.azure.com) et PowerShell. Et les professionnels de l’information peuvent utiliser les expériences du Volet d’accès à l’adresse http://myapps.microsoft.com. Les développeurs peuvent créer des applications à l’aide de l’API du Gestionnaire d’invitations Azure AD B2B pour ajouter des utilisateurs B2B Collaboration et personnaliser les flux de travail d’invitation et d’accueil.

Les utilisateurs B2B Collaboration sont généralement accueillis par le biais d’un processus d’invitation et d’échange. Voici comment cela fonctionne.

1. John Doe de WoodGrove souhaite ajouter Sam Oogle à l’aide de son adresse gmail (gsamoogle@gmail.com)

2. John accède au portail WoodGrove (portal.azure.com) ou au Volet d’accès (myapps.microsoft.com), se connecte et ajoute l’utilisateur au répertoire, au groupe ou à l’application WoodGrove.

3. John spécifie un e-mail d’invitation personnalisé à envoyer à Sam.

4. Dès qu’il a terminé, l’utilisateur suivant sera créé dans WoodGrove AD (capture d’écran issue de l’expérience utilisateur administrateur sur portal.azure.com) :

  ![utilisateur ajouté](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. Dès que nous avons terminé d’ajouter cet utilisateur, Azure AD envoie un e-mail d’invitation à Sam :

  ![e-mail d’invitation envoyé à Sam](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. À présent, Sam sélectionne **Commencer** et se connecte. À ce stade, Azure AD met à jour son objet utilisateur dans le répertoire avec les informations de son jeton (capture d’écran issue de l’expérience utilisateur administrateur dans le [portail Azure](https://portal.azure.com)) :

  ![profil utilisateur rempli](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

7. Maintenant que l’invitation de Sam a été utilisée, il peut accéder aux ressources de WoodGrove et, bien sûr, il peut être géré, comme tout autre utilisateur dans le répertoire, par l’administrateur (capture d’écran issue de l’expérience utilisateur administrateur dans le [portail Azure](https://portal.azure.com)) :

  ![Sam est désormais un utilisateur dans Azure AD](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>Fonctionnalités préliminaires publiques
Vous avez utilisé les fonctionnalités B2B Collaboration disponibles en version préliminaire publique et vous nous avez envoyés de nombreux commentaires utiles. Et nous vous avons écoutés ! Nous rassemblons toutes les améliorations que nous avons apportées dans cette actualisation de la version préliminaire publique. Voici les principales fonctionnalités dans l’actualisation de la version préliminaire publique de B2B Collaboration :

1. Améliorations de l’expérience utilisateur administrateur pour l’expérience B2B
  - à retrouver sur https://portal.azure.com
  - Possibilité pour les administrateurs d’inviter des utilisateurs B2B dans le répertoire, ou tout(e) groupe ou application

2. Fonctionnalités d’invitation libre-service B2B Collaboration pour les professionnels de l’information dans le Volet d’accès : https://myapps.microsoft.com. Les professionnels de l’information peuvent inviter des utilisateurs B2B Collaboration à rejoindre tout(e) groupe ou application libre-service qu’ils gèrent.

3. Vous pouvez désormais inviter un utilisateur avec n’importe quelle adresse e-mail. Que l’utilisateur dispose d’une adresse e-mail Office365 ou Microsoft Exchange sur site, d’une adresse e-mail outlook.com, de n’importe quelle adresse e-mail courante (Gmail, Yahoo, etc.), il peut désormais accéder de manière transparente à l’organisation invitée grâce à la création en ligne, légère, d’un compte Azure AD ou d’un compte Microsoft.

4. Avantages liés à un e-mail d’invitation professionnel, à la marque du client.

5. Capacité étendue de personnalisation de l’accueil à l’aide des API d’invitation.

6. Authentification multifacteur pour les utilisateurs B2B Collaboration dans l’organisation à l’origine de l’invitation.

7. Possibilité de déléguer les invitations aux utilisateurs non administrateurs.

8. Prise en charge de PowerShell pour B2B Collaboration.

9. Fonctionnalités d’audit et de création de rapports.

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [API et personnalisation d’Azure Active Directory B2B Collaboration](active-directory-b2b-api.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


