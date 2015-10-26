<properties
   pageTitle="Intégrer l’authentification unique Azure Active Directory aux applications récemment acquises | Microsoft Azure"
   description="Azure Active Directory prend en charge l'authentification unique pour les applications SaaS récemment acquises afin de permettre une gestion centralisée des accès dans le portail Azure"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/09/2015"
      ms.author="curtand"/>

# Intégration de l’authentification unique Azure Active Directory aux applications récemment acquises  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Pour commencer à configurer l'authentification unique pour une application que vous importez dans votre organisation, vous utiliserez un annuaire existant dans Azure Active Directory. Vous pouvez utiliser un annuaire Azure AD que vous obtenez via Microsoft Azure, Office 365 ou Windows Intune. Si vous avez deux ou plusieurs de ces éléments, consultez [Gestion de votre annuaire Azure AD](active-directory-administer.md) afin de déterminer celui à utiliser.

## Autres considérations

### Authentification

Pour les applications qui prennent en charge les protocoles SAML 2.0, WS-Federation ou OpenID Connect, Azure Active Directory utilise des certificats de signature afin d’établir des relations de confiance. Pour plus d'informations, consultez [Gestion des certificats pour l’authentification unique fédérée](active-directory-sso-certs.md).

Pour les applications qui prennent uniquement en charge l’authentification basée sur des formulaires HTML, Azure Active Directory utilise une technique appelée « password vaulting » (mise au coffre des mots de passe) pour établir des relations de confiance. Cela permet aux utilisateurs de votre organisation d’être automatiquement connectés à une application SaaS via Azure AD grâce aux informations de compte d’utilisateur de cette application SaaS. Azure AD recueille et stocke en toute sécurité les informations du compte d’utilisateur et le mot de passe qui y est associé. Pour plus d'informations, consultez [Authentification unique par mot de passe](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

### Autorisation

Un compte approvisionné est ce qui permet à un utilisateur d’être autorisé à utiliser une application, une fois qu’il est authentifié via l’authentification unique. L’approvisionnement de l'utilisateur peut s’effectuer manuellement, ou dans certains cas, vous pouvez ajouter et supprimer des informations sur l’utilisateur de l'application SaaS en fonction des modifications apportées dans Azure Active Directory. Pour plus d'informations sur l'utilisation de connecteurs Azure AD existants afin d’effectuer un approvisionnement automatisé, consultez [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)

Sinon, vous pouvez manuellement ajouter des informations sur l’utilisateur à une application, ou utiliser d'autres solutions d’approvisionnement disponibles sur Marketplace.

### Access

Azure AD offre plusieurs moyens personnalisables pour déployer des applications pour les utilisateurs finaux de votre organisation. Vous n’êtes pas restreint par une solution d'accès ou de déploiement particulière. Vous pouvez utiliser [la solution qui répond le mieux à vos besoins](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## Étapes suivantes

Pour les applications SaaS figurant déjà dans votre organisation et pour lesquelles vous souhaitez activer pour l’authentification unique, consultez [Intégration de l’authentification unique Azure Active Directory aux applications existantes](active-directory-sso-integrate-existing-apps.md)

Pour les applications SaaS figurant dans la galerie d'applications, Azure Active Directory fournit un certain nombre de [didacticiels sur la façon d'intégrer les applications SaaS](active-directory-saas-tutorial-list.md).

Si l'application n’apparaît pas dans la galerie d'applications, vous pouvez [l’ajouter à la galerie d’applications Azure Active Directory comme une application personnalisée](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Vous trouverez des informations beaucoup plus détaillées sur tous ces problèmes dans la bibliothèque Azure.com, en commençant par la rubrique [Accès aux applications et authentification unique avec Azure Active Directory.](active-directory-appssoaccess-whatis.md).

<!---HONumber=Oct15_HO3-->