---
title: "Accès conditionnel aux applications locales - Azure AD | Microsoft Docs"
description: "Explique comment configurer l&quot;accès conditionnel pour que les applications que vous publiez soient accessibles à distance à l’aide du proxy d&quot;application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 2e97722b-eb4e-4078-b607-9fed210d8a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: c16e07925389cc17ff156cae767366223fefa18f
ms.contentlocale: fr-fr
ms.lasthandoff: 04/28/2017


---
# <a name="working-with-conditional-access-in-azure-ad-application-proxy"></a>Utilisation de l’accès conditionnel dans le proxy d’application Azure AD
Vous pouvez configurer les règles d’accès pour accorder l’accès conditionnel aux applications publiées à l’aide du proxy d’application. Vous pouvez ainsi effectuer les opérations suivantes :

* Exiger l’authentification multifacteur par application.
* Exiger l’authentification multifacteur uniquement quand les utilisateurs se trouvent à l’extérieur de l’entreprise.
* Empêcher les utilisateurs d’accéder à l’application quand ils ne sont pas au travail.

Ces règles peuvent s’appliquer à tous les utilisateurs et à tous les groupes ou uniquement à des utilisateurs et à des groupes spécifiques. Par défaut, la règle s'applique à tous les utilisateurs qui ont accès à l'application. Cependant, la règle peut aussi se limiter aux utilisateurs membres de groupes de sécurité spécifiés.  

Les règles d’accès sont évaluées lorsqu’un utilisateur accède à une application fédérée qui utilise OAuth 2.0, OpenID Connect, SAML ou WS-Federation. De plus, les règles d’accès sont évaluées avec OAuth 2.0 et OpenID Connect quand un jeton d’actualisation est utilisé pour acquérir un jeton d’accès.

## <a name="conditional-access-prerequisites"></a>Conditions préalables d'accès conditionnel
* Abonnement à Azure Active Directory Premium
* Un locataire Azure Active Directory fédéré ou géré
* Les clients fédérés requièrent l’authentification multifacteur (MFA)  
    ![Configurer des règles d’accès - imposer l’authentification multifacteur](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Configurer l'authentification multifacteur pour chaque application
1. Connectez-vous au Portail Azure Classic en tant qu’administrateur.
2. Accédez à Active Directory, puis sélectionnez l’annuaire dans lequel vous souhaitez activer le proxy d’application.
3. Cliquez sur **Applications** et faites défiler jusqu’à la section **Règles d’accès**. La section des règles d’accès ne s’affiche que pour les applications publiées à l’aide du proxy d’application qui utilisent l’authentification fédérée.
4. Activez la règle en positionnant **Activer les règles d’accès** sur **Oui**.
5. Spécifiez les utilisateurs et les groupes auxquels les règles s’appliquent. Utilisez le bouton **Ajouter un groupe** pour sélectionner un ou plusieurs groupes auxquels s’applique la règle d’accès. Cette boîte de dialogue peut également servir à supprimer les groupes sélectionnés.  Lorsque les règles d’accès sont sélectionnées pour s’appliquer aux groupes, elles ne s’appliquent qu’aux utilisateurs qui appartiennent à un des groupes de sécurité spécifiés.  

   * Pour exclure explicitement des groupes de sécurité de la règle, cochez **Sauf** et spécifiez un ou plusieurs groupes. Les utilisateurs qui sont membres d’un groupe dans la liste Sauf ne sont pas tenus d’effectuer l’authentification multifacteur.  
   * Si un utilisateur a été configuré à l’aide de la fonctionnalité d’authentification multifacteur en fonction de l’utilisateur, ce paramètre a priorité sur les règles d’authentification multifacteur par application. Un utilisateur qui a été configuré pour l’authentification multifacteur en fonction de l’utilisateur doit effectuer l’authentification multifacteur, même s’il a été exclu des règles d’authentification multifacteur de l’application. En savoir plus sur [l’authentification multifacteur et sur les paramètres pour chaque utilisateur](../multi-factor-authentication/multi-factor-authentication.md).
6. Sélectionnez la règle d'accès que vous souhaitez définir :

   * **Exiger une authentification multifacteur** : les utilisateurs auxquels s’appliquent des règles d’accès doivent effectuer l’authentification multifacteur pour accéder à l’application à laquelle la règle s’applique.
   * **Exiger l’authentification multifacteur à l’extérieur de l’entreprise**: les utilisateurs qui tentent d’accéder à l’application à partir d’une adresse IP approuvée ne sont pas tenus d’effectuer l’authentification multifacteur. Les plages d'adresses IP approuvées peuvent être configurées sur la page des paramètres de l'authentification multifacteur.
   * **Bloquer l’accès quand l’utilisateur n’est pas au travail**: les utilisateurs qui tentent d’accéder à l’application en dehors de votre réseau d’entreprise ne sont pas en mesure d’accéder à l’application.

## <a name="configuring-mfa-for-federation-services"></a>Configuration de l'authentification multifacteur pour les services de fédération
Pour les clients fédérés, l’authentification multifacteur (MFA) peut être exécutée par Azure Active Directory ou par le serveur local AD FS. Par défaut, l’authentification multifacteur (MFA) a lieu sur n’importe quelle page hébergée par Azure Active Directory. Pour configurer l’authentification multifacteur localement, exécutez Windows PowerShell et utilisez la propriété –SupportsMFA pour configurer le module Azure AD.

L’exemple suivant montre comment activer l’authentification MFA localement à l’aide de [l’applet de commande Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) sur le client contoso.com : `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

En plus de définir cet indicateur, vous devez configurer l’instance AD FS du client fédéré pour effectuer une authentification multifacteur. Suivez les instructions fournies pour [déployer Microsoft Azure Multi-Factor Authentication en local](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="see-also"></a>Voir aussi
* [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
* [Publiez des applications avec le proxy d’application](active-directory-application-proxy-publish.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)

Pour les dernières nouvelles et mises à jour, visitez [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)

