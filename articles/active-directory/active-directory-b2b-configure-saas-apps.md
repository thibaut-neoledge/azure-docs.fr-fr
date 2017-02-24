---
title: Configurer des applications SaaS pour B2B Collaboration dans Azure Active Directory | Microsoft Docs
description: Code et exemples PowerShell pour Azure Active Directory B2B Collaboration
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 3f41fdaa4f0ec31c9f11f2826b5cb9ccbf4db30d


---

# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurer des applications SaaS pour B2B Collaboration

Azure Active Directory (Azure AD) B2B Collaboration fonctionne avec la plupart des applications qui s’intègrent à Azure AD. Dans cette section, nous allons vous fournir des instructions sur la configuration de certaines applications SAP populaires à utiliser avec Azure AD B2B.
Avant d’examiner les instructions spécifiques aux applications, voici quelques règles générales :

* N’oubliez pas que pour la plupart des applications, la configuration de l’utilisateur doit être effectuée manuellement (autrement dit, les utilisateurs doivent également être créés manuellement dans l’application).

* Pour les applications qui prennent en charge la configuration automatique (comme Dropbox), des invitations distinctes seront créées à partir des applications. L’utilisateur doit accepter chaque invitation.

* Dans les Attributs d’utilisateur, l’identificateur d’utilisateur doit toujours être défini sur user.mail (pour atténuer les problèmes liés aux UPD tronqués dans les utilisateurs invités)


##<a name="dropbox-for-business"></a>Dropbox for Business

Pour que les utilisateurs puissent se connecter à l’aide de leur compte professionnel, Dropbox for Business doit être configuré manuellement pour utiliser Azure AD comme fournisseur d’identité SAML. Dropbox for Business ne peut pas inviter ni autoriser les utilisateurs à se connecter à l’aide d’Azure AD s’il n’a pas été configuré pour ce faire.

1. Ajoutez l’application Dropbox for Business dans Azure AD, comme illustré dans la capture d’écran.

  ![ajouter Dropbox dans Azure AD](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

  ![ajouter Dropbox dans Azure AD](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

2. Configurez l’application.

  ![Configurer l’authentification unique pour l’application](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

3. Sélectionnez la configuration de l’authentification unique et modifiez l’identificateur d’utilisateur sur user.mail (son UPN par défaut)

4. Téléchargez le certificat à utiliser pour la configuration de Dropbox.

  ![télécharger le certificat](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Utilisez l’option Configurer Dropbox (le processus est expliqué en détail dans les captures d’écran ci-dessous)

6. Obtenez l’URL de l’authentification unique SAML à utiliser dans la configuration.

7. Obtenez l’URL de connexion à partir de la page de configuration de Dropbox.

  ![connexion à dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

  ![Menu Dropbox](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

  ![Boîte de dialogue d’authentification Dropbox réduite](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![Boîte de dialogue d’authentification Dropbox développée](media/active-directory-b2b-configure-saas-apps/dropbox-auth-02.png)

8. Chargez le certificat et collez l’URL d’authentification unique SAML ici.

  ![Coller l’URL d’authentification unique SAML](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

9. Configurez l’approvisionnement d’utilisateurs automatique dans le portail Azure.

  ![configurer l’approvisionnement d’utilisateurs automatique](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

10. Une fois configuré dans l’application Dropbox, l’utilisateur invité/membre reçoit une invitation distincte de la part de Dropbox. Les invités doivent l’accepter en cliquant sur le lien, pour utiliser l’authentification unique dans Dropbox.

## <a name="box"></a>Box
Cette section explique comment permettre aux utilisateurs d’authentifier un utilisateur invité sur Box avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez charger un certificat vers Box.com.

1. Ajouter Box à partir des applications d’entreprise

2. Configurer l’authentification unique

  ![Configurer l’authentification unique de Box](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

3. Tout d’abord, assurez-vous que l’URL de connexion est définie correctement pour Box dans le portail de gestion Azure. Il s’agit de l’URL de votre client Box.com qui doit être au format : https://.box.com.

4. Sachez que l’identificateur n’est pas applicable pour cette application, mais il s’affiche tout de même en tant que champ obligatoire.

5. Identificateur d’utilisateur défini sur user.mail (pour activer l’authentification unique pour les comptes invités)

6. Créer un nouveau certificat SAML

7. Pour configurer votre client Box.com afin qu’il utilise Azure Active Directory comme fournisseur d’identité, commencez par télécharger le fichier de métadonnées suivant et enregistrez-le localement sur votre ordinateur : Télécharger le fichier de métadonnées (veillez à le rendre actif)

8. Transférez ce fichier de métadonnées à l'équipe de support Box. L’équipe de support configure l’authentification unique pour vous.

9. Configurez l’approvisionnement d’utilisateurs automatique Azure AD.

  ![Autoriser Azure AD à se connecter à Box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Les invités doivent également utiliser leur invitation reçue de l’application Box.

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriétés de l’utilisateur B2B Collaboration](active-directory-b2b-user-properties.md)
* [Ajout d’un utilisateur B2B Collaboration à un rôle](active-directory-b2b-add-guest-to-role.md)
* [Déléguer des invitations B2B Collaboration](active-directory-b2b-delegate-invitations.md)
* [Groupes dynamiques et B2B Collaboration](active-directory-b2b-dynamic-groups.md)
* [Code B2B Collaboration et exemples PowerShell](active-directory-b2b-code-samples.md)
* [Jetons utilisateur B2B Collaboration](active-directory-b2b-user-token.md)
* [Mappage des revendications utilisateur B2B Collaboration](active-directory-b2b-claims-mapping.md)
* [Partage externe d’Office 365](active-directory-b2b-o365-external-user.md)
* [Limitations actuelles de B2B Collaboration](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


