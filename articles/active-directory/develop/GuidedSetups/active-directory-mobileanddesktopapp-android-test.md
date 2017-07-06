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
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 8523f46d6a352c9a6625ddeacc5abe2b4bbf977e
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


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
### <a name="more-information-about-scopes-and-delegated-permissions"></a>En savoir plus sur les étendues et les autorisations déléguées
L’API Graph nécessite que l’étendue `user.read` lise le profil utilisateur. Cette étendue est ajoutée par défaut à chaque application enregistrée sur notre portail d’enregistrement. D’autres API Graph ainsi que des API personnalisées pour votre serveur principal nécessitent des étendues supplémentaires. Par exemple, pour Graph, `Calendars.Read` est requis afin de répertorier les calendriers de l’utilisateur. Pour accéder au calendrier de l’utilisateur dans le contexte d’une application, vous devez ajouter les informations d’inscription de cette application déléguée, puis ajouter `Calendars.Read` à l’appel `AcquireTokenAsync`. L’utilisateur peut être invité à redonner son consentement lorsque vous augmentez le nombre d’étendues.

Si une API principale ne nécessite pas d’étendue (non recommandé), vous pouvez utiliser `ClientId` en tant qu’étendue dans l’appel `AcquireTokenAsync`.
<!--end-collapse-->

