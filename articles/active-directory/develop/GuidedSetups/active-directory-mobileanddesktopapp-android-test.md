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
ms.openlocfilehash: b59c44e351c3b3aa74557d0d1625495277b1bc1a
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2017
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

L’API Microsoft Graph nécessite l’étendue `user.read` pour lire le profil de l’utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans notre portail d’inscription. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. Par exemple, pour Microsoft Graph, l’étendue `Calendars.Read` est nécessaire pour dresser la liste des calendriers de l’utilisateur. Pour accéder au calendrier de l’utilisateur dans le contexte d’une application, vous devez ajouter l’autorisation déléguée `Calendars.Read` aux informations d’inscription de l’application, puis ajouter l’étendue `Calendars.Read` à l’appel `acquireTokenSilentAsync`. L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

<!--end-collapse-->

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]
