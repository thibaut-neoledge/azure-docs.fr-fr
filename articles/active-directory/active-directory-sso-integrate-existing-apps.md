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
   ms.date="10/16/2015"
   ms.author="kgremban; liviodlc"/>

# Intégration de l’authentification unique Azure Active Directory aux applications existantes

## Vue d'ensemble

Configurer l'authentification unique (SSO) pour une application que votre organisation utilise déjà est un processus différent de la création de comptes pour une nouvelle application. Il existe quelques étapes préliminaires, notamment : mapper les identités d'utilisateurs dans l'application à des identités Azure Active Directory (AD), et comprendre l’expérience de connexion des utilisateurs à une application après son intégration.

> [AZURE.NOTE]Afin de configurer l'authentification unique pour une application existante, vous devez disposer des droits d'administrateur global à la fois pour Azure AD et l'application SaaS.

## Mappage de comptes d’utilisateurs

L’identité d'un utilisateur comporte généralement un identificateur unique qui peut être une adresse de messagerie ou un nom universel (UPN). Vous devrez lier (mapper) l’identité d'application de chaque utilisateur à son identité Azure AD correspondante. Il existe deux façons de procéder selon les exigences d'authentification de votre application.

Pour plus d'informations sur le mappage d’identités d'application avec des identités Azure AD, consultez [Personnalisation des demandes émises dans le jeton SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) et [Personnalisation des mappages d'attributs pour le provisionnement](active-directory-saas-customizing-attribute-mappings.md).

## Comprendre l’expérience de connexion de l'utilisateur

Lorsque vous intégrez l'authentification unique pour une application en cours d'utilisation, il est important de savoir que l'expérience utilisateur en sera affectée. Pour toutes les applications, les utilisateurs commenceront à se servir de leurs informations d'identification Azure AD pour se connecter. En outre, ils devront peut-être utiliser un autre portail pour accéder aux applications.

L'authentification unique pour certaines applications peut être effectuée sur l’interface de connexion de l'application, mais pour d'autres applications, l’utilisateur devra passer par un portail central ([My Apps](http://myapps.microsoft.com) ou [Office 365](http://portal.office.com/myapps)) pour se connecter. Pour en savoir plus sur les différents types d'authentification unique et leurs expériences utilisateur, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

Voir aussi *Suppression du consentement de l’utilisateur* dans le [guide pour les développeurs](active-directory-applications-guiding-developers-for-lob-applications.md).

## Articles connexes
liens vers la suppression du consentement de l'utilisateur et d'autres articles secondaires du guide pour les développeurs

<!---HONumber=Oct15_HO4-->