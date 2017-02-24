---
title: "Offrir un accès à distance sécurisé aux applications locales"
description: "Explique comment utiliser le Proxy d’application Azure AD pour offrir un accès à distance sécurisé à vos applications locales."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2e7815702f2d2f4ce935826c4769838727a83696
ms.openlocfilehash: 7d1be1dea6ed4ecda196743f592456a5b977e9b0


---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Offrir un accès à distance sécurisé aux applications locales
> [!NOTE]
> Le proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
> 
> 

Aujourd’hui, les employés veulent être productifs partout, tout le temps, depuis n’importe quel appareil. Ils veulent pouvoir travailler sur leurs propres appareils (tablettes, téléphones ou ordinateurs portables). Ils veulent aussi pouvoir accéder à toutes leurs applications, qu’il s’agisse d’applications SaaS dans le cloud ou d’applications métier sur le réseau local de l’entreprise. La fourniture d’un accès aux applications locales implique généralement des réseaux privés virtuels (VPN), des zones démilitarisées (DMZ) ou des proxys inverses locaux. Non seulement ces solutions sont complexes et difficiles à sécuriser, mais leur configuration et leur gestion ont un coût élevé.

Il y a une meilleure solution.

Le monde du travail mobile actuel axé sur le cloud exige une solution d’accès à distance moderne. Le Proxy d’application Azure AD est une fonctionnalité de l’offre Azure Active Directory Premium qui fournit un accès à distance en tant que service. Il est donc facile à déployer, à utiliser et à gérer.

## <a name="what-is-azure-active-directory-application-proxy"></a>Présentation du Proxy d’application Azure Active Directory
Le Proxy d’application Azure AD offre une authentification unique (SSO) et un accès à distance sécurisé pour les applications web hébergées en local. Cela peut inclure les sites SharePoint, Outlook Web Access ou toute autre application web métier dont vous disposez. Ces applications web locales sont intégrées à Azure AD, la même plateforme d’identité et de contrôle que celle utilisée par O365. Les utilisateurs peuvent ensuite accéder aux applications locales de la même manière qu’ils accèdent à O365 et à d’autres applications SaaS intégrées à Azure AD. Il n’est pas nécessaire de modifier l’infrastructure réseau ou de disposer d’un VPN pour pouvoir proposer cette solution à vos utilisateurs.

## <a name="why-is-this-a-better-solution"></a>En quoi est-ce une meilleure solution ?
Le Proxy d’application Azure AD fournit une solution d’accès à distance simple, sécurisée et rentable à toutes vos applications locales.

Activation du Proxy d’application Azure AD :  

* Fonctionne dans le cloud pour que vous puissiez gagner du temps et faire des économies. Les solutions locales vous obligent à définir et à gérer les zones démilitarisées, les serveurs Edge ou les autres infrastructures complexes.  
* Est plus facile à configurer et à sécuriser que les solutions locales, car vous n’êtes pas obligé d’ouvrir toutes les connexions entrantes via votre pare-feu.  
* Offre une excellente sécurité. Lorsque vous publiez vos applications à l’aide du Proxy d’application Azure AD, vous pouvez tirer parti des nombreux contrôles d’autorisation et des analyses de sécurité dans Azure. Cela signifie que vous profitez de capacités de sécurité avancée pour toutes vos applications existantes sans avoir à les modifier.  
* Permet à vos utilisateurs de bénéficier d’une expérience d’authentification cohérente. L’authentification unique permet aux utilisateurs finaux d’accéder facilement et simplement à toutes les applications dont ils ont besoin pour être productifs avec un seul mot de passe.  

## <a name="what-kind-of-applications-work-with-azure-ad-application-proxy"></a>Quels types d’applications fonctionnent avec le Proxy d’application Azure AD ?
Avec le Proxy d’application Azure AD, vous pouvez accéder à différents types d’applications internes :

* Applications web qui utilisent l’authentification Windows intégrée pour l’authentification  
* Applications web qui utilisent l’accès basé sur un formulaire  
* API web que vous voulez exposer aux applications enrichies sur différents appareils  
* Applications hébergées derrière une passerelle Bureau à distance  

## <a name="how-does-the-service-work-with-connectors"></a>Comment le service fonctionne-t-il avec des connecteurs ?
Le Proxy d’application fonctionne grâce à l’installation d’un service Windows Server léger appelé connecteur au sein de votre réseau. Avec le connecteur, vous n’êtes pas obligé d’ouvrir des ports entrants ou de placer quoi que ce soit dans la zone dématérialisée. Si le volume de trafic vers vos applications est important, vous pouvez ajouter d’autres connecteurs. Le service se charge de l’équilibrage de la charge. Les connecteurs sont sans état et extraient tout ce dont ils ont besoin sur le cloud.

Pour plus d’informations sur les connecteurs, consultez [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md). 

Lorsque les utilisateurs accèdent à distance à des applications, ils se connectent au point de terminaison publié. Les utilisateurs s’authentifient dans Azure AD puis sont acheminés via le connecteur vers l’application locale.

 ![Diagramme du Proxy d’application Azure AD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. L’utilisateur accède à l’application via le Proxy d’application et est dirigé vers la page de connexion Azure AD pour s’identifier.
2. Après la connexion, un jeton est généré et envoyé à l’utilisateur.
3. L’utilisateur envoie le jeton au Proxy d’application qui récupère le nom d’utilisateur principal (UPN) et le nom de sécurité principal (SPN) du jeton, puis dirige la requête vers le connecteur.
4. Le connecteur emprunte l'identité de l'utilisateur pour demander un ticket Kerberos qui peut être utilisé pour l'authentification (Windows) interne. Ce processus est appelé délégation Kerberos contrainte
5. Active Directory récupère le ticket Kerberos.
6. Le ticket est envoyé au serveur d’applications puis vérifié.
7. La réponse est envoyée à l’utilisateur via le Proxy d’application.

### <a name="single-sign-on"></a>Authentification unique
Le Proxy d’application Azure AD fournit l’authentification unique (SSO) aux applications qui utilisent l’authentification Windows intégrée (IWA) ou aux applications prenant en charge les revendications. Si votre application utilise l’authentification IWA, le Proxy d’application emprunte l’identité de l’utilisateur à l’aide de la délégation Kerberos contrainte pour fournir l’authentification unique. Si vous avez une application prenant en charge les revendications qui fait confiance à Azure Active Directory, l’authentification unique est réussie, car l’utilisateur a déjà été authentifié par Azure AD.

Pour plus d’informations sur Kerberos, consultez [All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd) (Tout ce que vous voulez savoir sur Kerberos Constrained Delegation (KCD)).

## <a name="how-to-get-started"></a>Pour commencer
Assurez-vous que vous disposez d’un abonnement Azure AD Basic ou Premium et d’un annuaire Azure AD sur lequel vous êtes administrateur global. Vous devez également disposer de licences d’Azure AD Premium ou Basic pour l’administrateur de l’annuaire et les utilisateurs qui accèdent aux applications. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md) .

La configuration du Proxy d’application s’effectue en deux étapes :

1. [Activer le proxy d’application et configurer le connecteur](active-directory-application-proxy-enable.md).    
2. [Publier des applications](active-directory-application-proxy-publish.md) : utilisez l’Assistant simple et rapide pour publier vos applications locales et les rendre accessibles à distance.

## <a name="whats-next"></a>Et ensuite ?
Vous pouvez faire bien d’autres choses encore avec le Proxy d’application :

* [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
* [Utiliser des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md) 
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
* [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)

Pour les dernières nouvelles et mises à jour, consultez le site [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Feb17_HO2-->


