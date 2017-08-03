---
title: "Prise en main d’Azure AD v2 Android - Test | Microsoft Docs"
description: "Cet article explique comment une application Android peut obtenir un jeton d’accès et appeler une ou plusieurs API Microsoft Graph qui nécessitent des jetons d’accès à partir du point de terminaison Azure Active Directory v2"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 6df64f4820f8409bd8897d5ac24f81bffeeef102
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017


---
## <a name="test-your-code"></a>Test de votre code

1. Déployez votre code sur votre appareil/émulateur.
2. Lorsque vous êtes prêt pour le test, utilisez un compte Microsoft Azure Active Directory (compte de société) ou un compte Microsoft (live.com, outlook.com) pour vous connecter. 

![Exemple d’écran](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![Connexion](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>Consentement
La première fois qu’un utilisateur se connecte à votre application, un écran de consentement semblable à ce qui suit s’affiche. L’utilisateur doit accepter explicitement : 

![Consentement](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>Résultats attendus
Vous devez voir les résultats d’un appel au point de terminaison me de l’API Microsoft Graph pour obtenir le profil utilisateur : https://graph.microsoft.com/v1.0/me. Pour obtenir la liste des points de terminaison Microsoft Graph les plus courants, consultez cet [article](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph requiert l’étendue `user.read` pour lire le profil utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans notre portail d’inscription. D’autres API Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. Par exemple, pour Microsoft Graph, l’étendue `Calendars.Read` est requise pour dresser la liste des calendriers de l’utilisateur. Pour accéder au calendrier de l’utilisateur dans le contexte d’une application, vous devez ajouter l’autorisation déléguée `Calendars.Read` aux informations d’inscription de l’application, puis ajouter l’étendue `Calendars.Read` à l’appel `acquireTokenSilentAsync`. L’utilisateur peut être invité à donner des consentements supplémentaires lorsque vous augmentez le nombre d’étendues.

<!--end-collapse-->

