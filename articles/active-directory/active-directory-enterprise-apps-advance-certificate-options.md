---
title: "Options avancées de signature de certificats dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory | Microsoft Docs"
description: "Découvrez comment utiliser les options avancées de signature de certificats dans le jeton SAML pour les applications pré-intégrées dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 70e495965287a0edcb31493b69311fe28e04f6dc
ms.contentlocale: fr-fr
ms.lasthandoff: 09/19/2017

---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Options avancées de signature de certificats dans le jeton SAML pour les applications de galerie dans Azure Active Directory
Aujourd’hui, Azure Active Directory prend en charge des milliers d’applications pré-intégrées dans la galerie d’applications Azure AD, notamment plus de 500 applications qui gèrent l’authentification unique à l’aide du protocole SAML 2.0. Quand un utilisateur s’authentifie auprès d’une application par l’intermédiaire d’Azure AD en utilisant SAML, Azure AD envoie un jeton à l’application (via HTTP POST). Après quoi, l’application valide et utilise ce jeton pour connecter l’utilisateur au lieu de lui demander un nom d’utilisateur et un mot de passe. Ces jetons SAML sont signés avec le certificat unique qui est généré dans Azure AD. Ce jeton SAML est signé avec les algorithmes standards.

Azure Active Directory utilise certains des paramètres par défaut pour les applications de la galerie. Les valeurs par défaut sont configurées selon les exigences de l’application.

Azure Active Directory prend en charge des paramètres avancés de signature de certificat. Pour sélectionner ces options, sélectionnez d’abord la case à cocher **Paramètres avancées de signature du certificat d’avance SAML** comme indiqué ci-dessous.

![Options de signature de certificat][1]

Une fois cette case cochée, vous pouvez configurer les **options de signature de certificat** et **l’algorithme de signature de certificat**.

## <a name="certificate-signing-options"></a>Options de signature de certificat

Voici les trois options de signature de certificat prises en charge par Azure AD.

1. **Signature des assertions SAML** -cette option est la valeur par défaut définie pour la plupart des applications de la galerie. Si cette option est sélectionnée, Azure AD en tant que IDP signe les assertions et le certificat SAML avec le certificat X509 de l’application. Il utilise également l’algorithme de signature sélectionné dans la liste déroulante ci-dessous.

2. **Signature de la réponse SAML** - Si cette option est sélectionnée, Azure AD en tant que IDP signe la réponse SAML avec le certificat X509 de l’application. Il utilise également l’algorithme de signature sélectionné dans la liste déroulante ci-dessous.

3. **Signature de la réponse et de l’assertion SAML** - Si cette option est sélectionnée, Azure AD en tant que IDP signe le jeton SAML entier avec le certificat X509 de l’application. Il utilise également l’algorithme de signature sélectionné dans la liste déroulante ci-dessous.

    ![Options de signature de certificat][4]

## <a name="certificate-signing-algorithm"></a>Algorithme de signature de certificat

Azure Active Directory prend en charge deux types d’algorithme de signature pour signer la réponse SAML.

1. SHA256 - il s’agit de l’algorithme par défaut utilisé par Azure Active Directory pour signer la réponse SAML. Il s’agit de l’algorithme le plus récent et considéré comme plus sécurisé que le SHA1. La plupart des applications prennent en charge l’algorithme SHA256. Si l’application prend uniquement en charge l’algorithme SHA1, vous pouvez le modifier. Dans le cas contraire, nous vous recommandons d’utiliser l’algorithme SHA256 pour la signature de la réponse SAML.

    ![Algorithme de signature de certificat SHA256][3]

2. SHA1 - Il s’agit de l’ancien algorithme, considéré comme non sécurisé. Si l’application prend uniquement en charge cet algorithme de signature, vous pouvez sélectionner cette option dans la liste déroulante. Azure AD signe alors la réponse SAML avec l’algorithme SHA1.

    ![Algorithme de signature de certificat SHA1][2]

## <a name="next-steps"></a>Étapes suivantes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory.](active-directory-saas-custom-apps.md)
* [Dépannage de l’authentification unique basée sur SAML](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png
