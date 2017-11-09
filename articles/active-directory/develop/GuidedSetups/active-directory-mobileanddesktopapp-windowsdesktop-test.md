---
title: "Prise en main d’Azure AD v2 avec les applications de bureau Windows - Test | Documents Microsoft"
description: "Cet article explique comment les applications de bureau Windows .NET (XAML) peuvent appeler une API qui nécessite des jetons d’accès à partir d’un point de terminaison Azure Active Directory v2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 972cc48057c13271d725b0c973c3ccf651ad27c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Test de votre code

Pour tester votre application, appuyez sur `F5` afin d’exécuter votre projet dans Visual Studio. Votre fenêtre principale doit alors s’afficher :

![Exemple d’écran](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

Lorsque vous êtes prêt pour le test, cliquez sur *Call Microsoft Graph API* (Appeler l’API Microsoft Graph) et utilisez un compte Microsoft Azure Active Directory (compte de société) ou un compte Microsoft (live.com, outlook.com) pour vous connecter. S’il s’agit de la première fois, une fenêtre vous invitant à vous connecter s’affiche :

![Connexion](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>Consentement
La première fois que vous vous connectez à votre application, un écran de consentement semblable à ce qui suit, où vous devez accepter explicitement ce qui est indiqué, s’affiche :

![Écran de consentement](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>Résultats attendus
Vous devez voir les informations de profil utilisateur renvoyées par l’appel de l’API Microsoft Graph dans l’écran API Call Results (Résultats de l’appel d’API).

Des informations de base sur le jeton obtenu via `AcquireTokenAsync` ou `AcquireTokenSilentAsync` doivent également s’afficher dans la zone Token Info (Informations sur le jeton) :

|Propriété  |Format  |Description |
|---------|---------|---------|
|Nom | {Nom complet de l’utilisateur} |Prénom et nom de l’utilisateur.|
|Nom d’utilisateur |<span>user@domain.com</span> |Nom d’utilisateur employé pour identifier l’utilisateur.|
|Token Expires (Expiration du jeton) |{DateHeure}         |Heure à laquelle le jeton expire. MSAL étend la date d’expiration pour vous en renouvelant le jeton dès que nécessaire.|
|Access token (Jeton d’accès) |{Chaîne}         |Chaîne de jeton qui sera envoyée aux requêtes HTTP qui nécessitent un en-tête d’autorisation.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées
L’API Graph nécessite que l’étendue `user.read` lise le profil utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans notre portail d’inscription. D’autres API Graph ainsi que des API personnalisées pour votre serveur principal nécessitent des étendues supplémentaires. Par exemple, pour Graph, `Calendars.Read` est requis afin de répertorier les calendriers de l’utilisateur. Pour accéder au calendrier de l’utilisateur dans le contexte d’une application, vous devez ajouter les informations d’inscription de l’application déléguée `Calendars.Read`, puis ajouter `Calendars.Read` à l’appel `AcquireTokenAsync`. L’utilisateur peut être invité à donner des consentements supplémentaires lorsque vous augmentez le nombre d’étendues.

<!--end-collapse-->



