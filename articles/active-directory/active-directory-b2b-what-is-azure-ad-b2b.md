---
title: "À propos de la version préliminaire d’Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise."
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
ms.date: 02/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 1f6ce516629fd32b5efabce8682b45a3e575eaab
ms.lasthandoff: 03/10/2017


---

# <a name="about-the-azure-ad-b2b-collaboration-preview"></a>À propos de la version préliminaire d’Azure AD B2B Collaboration
Cet article décrit les objectifs, les fonctionnalités et les avantages de l’actualisation de la version préliminaire publique d’Azure Active Directory (Azure AD) B2B Collaboration.

Que vous soyez un professionnel de l’informatique ou de l’information, vous pouvez tirer parti des nombreuses fonctionnalités d’Azure AD B2B Collaboration. Vous pouvez travailler en étroite collaboration avec des partenaires d’autres organisations, indépendamment de leur localisation, de leur taille, de leur secteur d’activité ou de leurs exigences de conformité et de gouvernance. Vous pouvez aussi leur donner accès à des documents, des ressources et des applications tout en conservant le contrôle de vos données internes.

Si vous êtes un développeur, vous pouvez utiliser les API Azure AD B2B pour écrire des applications qui rapprochent les organisations en toute sécurité. Pour les professionnels de l’information, le processus et la navigation sont simples.

## <a name="how-b2b-collaboration-works"></a>Fonctionnement de B2B Collaboration

Dans la version préliminaire actuelle, les professionnels de l’informatique et de l’information établissent des relations avec des organisations partenaires en ajoutant leurs utilisateurs (individuellement ou plusieurs à la fois) à l’aide du portail Azure ou de l’API du Gestionnaire d’invitations.

Les administrateurs peuvent utiliser les nouvelles expériences du portail dans le portail Azure (https://portal.azure.com) et PowerShell pour établir des relations.

Les professionnels de l’information peuvent utiliser les expériences du volet d’accès à l’adresse http://myapps.microsoft.com.

Les développeurs peuvent créer des applications à l’aide de l’API du Gestionnaire d’invitations Azure AD B2B pour ajouter des utilisateurs B2B Collaboration et personnaliser les flux de travail d’invitation et d’accueil.

Les utilisateurs B2B Collaboration sont généralement ajoutés par le biais d’un processus d’invitation et d’échange. Voici comment cela fonctionne :

1. John Doe de WoodGrove souhaite ajouter Sam Oogle en tant qu’utilisateur à l’aide de son adresse Gmail (gsamoogle@gmail.com).

2. John Doe accède au portail WoodGrove (portal.azure.com) ou au volet d’accès (myapps.microsoft.com), se connecte et ajoute l’utilisateur Sam Oogle au répertoire, au groupe ou à l’application WoodGrove.

3. John Doe spécifie un e-mail d’invitation personnalisé à envoyer à Sam Oogle.

4. Lorsque John Doe envoie l’invitation, l’utilisateur Sam Oogle est créé dans Azure AD pour WoodGrove, comme illustré ici :

  ![Interface d’utilisateur admin dans portal.azure.com](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. Une fois l’utilisateur créé, Azure AD envoie un e-mail d’invitation à Sam Oogle :

  ![E-mail d’invitation envoyé à Sam Oogle](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. Dans l’invitation, Sam Oogle sélectionne **Prise en main**.  
Le portail Azure s’ouvre.

7. SAM Oogle se connecte au portail Azure.

8. Azure Active Directory met à jour l’objet utilisateur Sam Oogle dans Azure AD avec les informations de son jeton, comme illustré ici :

  ![Profil utilisateur de Sam Oogle dans le portail Azure](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

9. Maintenant que l’utilisateur Sam Oogle a utilisé l’invitation, il peut accéder aux ressources de WoodGrove. Comme tout autre utilisateur dans Azure AD, l’utilisateur Sam Oogle peut être géré par l’administrateur. La liste d’utilisateurs est illustrée ici :

  ![Liste d’utilisateurs Azure AD](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>Fonctionnalités de la version préliminaire publique
Selon les commentaires des utilisateurs, l’actualisation de la version préliminaire publique de B2B Collaboration fournit les fonctionnalités clés suivantes :

* Pour les administrateurs : obtenir des améliorations de l’interface utilisateur dans le [portail Azure](https://portal.azure.com). Par exemple, les administrateurs peuvent inviter des utilisateurs B2B dans le répertoire, ou tout(e) groupe ou application.  

* Pour les professionnels de l’information : obtenir des fonctionnalités d’invitation libre-service B2B Collaboration dans le [volet d’accès](https://myapps.microsoft.com). Les professionnels de l’information peuvent inviter des utilisateurs B2B Collaboration à rejoindre tout(e) groupe ou application libre-service qu’ils gèrent.

* Autoriser les utilisateurs invités à avoir une adresse de messagerie. Qu’il s’agisse d’une adresse Office 365 ou d’une adresse Microsoft Exchange locale, d’une adresse outlook.com ou d’une adresse de réseau social (Gmail, Yahoo! et ainsi de suite), les utilisateurs peuvent accéder à l’organisation invitée avec la création d’un compte Azure AD ou Microsoft.

* Créer un e-mail d’invitation professionnel, à la marque du client.

* Personnaliser l’orientation de l’utilisateur à l’aide d’API d’invitation.

* Configurer une authentification multifacteur pour les utilisateurs B2B Collaboration dans l’organisation à l’origine de l’invitation.

* Déléguer les invitations aux utilisateurs non administrateurs.

* Fournir la prise en charge de PowerShell pour B2B Collaboration.

* Fournir des fonctionnalités d’audit et de création de rapports.

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
* [B2B collaboration user auditing and reporting](active-directory-b2b-auditing-and-reporting.md) (Audit et création de rapports relatifs aux utilisateurs B2B Collaboration)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

