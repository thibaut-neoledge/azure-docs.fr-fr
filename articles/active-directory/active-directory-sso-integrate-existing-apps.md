<properties
   pageTitle="Intégration de l’authentification unique Azure Active Directory aux applications existantes | Microsoft Azure"
   description="Gestion des applications SaaS que vous utilisez déjà en activant l'authentification unique et l’approvisionnement de l'utilisateur dans Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/05/2015"
   ms.author="kgremban; liviodlc"/>

# Intégration de l’authentification unique Azure Active Directory aux applications existantes

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

## Autres considérations

La configuration de l'authentification unique pour les applications que vous utilisez déjà est un processus différent de la création de nouveaux comptes dans les nouvelles applications. Les administrateurs et les utilisateurs constateront un changement lorsque l'application est intégrée à Azure AD.

### Que devront savoir les administrateurs afin de configurer l'authentification unique pour les applications en cours d'utilisation ?

Afin de configurer l'authentification unique pour une application existante, vous devez disposer des droits d'administrateur global à la fois pour Azure AD et l'application SaaS.

Comme l'application est déjà en cours d'utilisation, vous devrez lier des identités d'application établies par l'utilisateur à leurs identités Azure AD respectives. Il est important de savoir ce que cette application utilise comme identificateur unique pour l'authentification, qu'il s'agisse d'une adresse de messagerie, d’un nom universel (UPN) ou d’un nom d'utilisateur. Ces informations seront liées à l'identificateur unique de l'utilisateur dans Azure AD. Pour plus d'informations sur la liaison d’identités d'application avec des identités Azure AD, consultez [Personnalisation des demandes émises dans le jeton SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) et [Personnalisation des mappages d'attributs pour le provisionnement](active-directory-saas-customizing-attribute-mappings.md).

### En quoi cela affecte-t-il les utilisateurs finaux ?

Lorsque vous intégrez l'authentification unique pour une application en cours d'utilisation, il est important de savoir que l'expérience utilisateur en sera affectée. Pour toutes les applications, les utilisateurs commenceront à se servir de leurs informations d'identification Azure AD pour se connecter. En outre, ils devront peut-être utiliser un autre portail pour accéder aux applications. L'authentification unique pour certaines applications peut être effectuée sur le site Web de l'application, mais pour d'autres applications, l’utilisateur devra passer par un portail d’application central ([My Apps](myapps.microsoft.com) ou [Office 365](portal.office.com/myapps)) pour se connecter. Pour en savoir plus sur les différents types d'authentification unique et leurs expériences utilisateur, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Étapes suivantes
- Découvrir comment [intégrer l’authentification unique Azure Active Directory aux applications récemment acquises](active-directory-sso-newly-acquired-saas-apps.md)
- Obtenir plus d’informations sur [l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md)
- Rechercher [des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)
-	Ajout d'une application personnalisée avec [la configuration SAML en libre service Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)

<!---HONumber=Oct15_HO3-->