---
title: "Configuration guidée d’Azure AD v2 JS SPA - Test | Microsoft Docs"
description: "Comment les applications JavaScript SPA peuvent appeler une API qui nécessite des jetons d’accès à partir d’un point de terminaison Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: c888760ab311e8ac08b1e625bb837f91047db645
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Test de votre code

> ### <a name="testing-with-visual-studio"></a>Test avec Visual Studio
> Si vous utilisez Visual Studio, appuyez sur `F5` pour exécuter votre projet : le navigateur s’ouvre et vous dirige vers la page *http://localhost:{port}* qui contient le bouton *Call Microsoft Graph API* (Appeler l’API Microsoft Graph).

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>Test avec Python ou un autre serveur web
> Si vous n’utilisez pas Visual Studio, vérifiez que votre serveur web est démarré et qu’il est configuré pour écouter un port TCP basé sur le dossier contenant votre fichier *index.html*. Pour Python, vous pouvez démarrer l’écoute sur le port via le terminal/l’invite de commandes, à partir du dossier de l’application :
> 
> ```bash
> python -m http.server 8080
> ```
>  Ouvrez ensuite le navigateur et saisissez *http://localhost:8080* ou *http://localhost:{port}*, où *port* correspond au port qu’écoute votre serveur web. Vous devriez voir le contenu de votre page index.html et le bouton *Call Microsoft Graph API* (Appeler l’API Microsoft Graph).

## <a name="test-your-application"></a>Tester votre application

Une fois votre fichier *index.html* chargé dans le navigateur, cliquez sur le bouton *Call Microsoft Graph API* (Appeler l’API Microsoft Graph). S’il s’agit du premier chargement, le navigateur vous redirige vers le point de terminaison Microsoft Azure Active Directory v2, où vous êtes invité à vous connecter.
 
![Exemple d’écran](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Consentement
La première fois que vous vous connectez à votre application, un écran de consentement semblable à ce qui suit, où vous devez accepter ce qui est indiqué, s’affiche :

 ![Écran de consentement](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>Résultats attendus
Vous devez voir les informations de profil utilisateur retournées par l’appel de l’API Microsoft Graph.
 
 ![Résultats](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

Vous verrez également des informations de base sur le jeton acquis sous les sections *Access Token* (Jeton d’accès) et *ID Token Claims* (Revendications de jetons d’ID).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue `user.read` pour lire le profil de l’utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans notre portail d’inscription. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. Par exemple, pour Microsoft Graph, l’étendue `Calendars.Read` est nécessaire pour dresser la liste des calendriers de l’utilisateur. Pour accéder au calendrier de l’utilisateur dans le contexte d’une application, vous devez ajouter l’autorisation déléguée `Calendars.Read` aux informations d’inscription de l’application, puis ajouter l’étendue `Calendars.Read` à l’appel `acquireTokenSilent`. L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

Si une API principale ne nécessite pas d’étendue (non recommandé), vous pouvez utiliser le `clientId` en tant qu’étendue dans les appels `acquireTokenSilent` et/ou `acquireTokenRedirect`.

<!--end-collapse-->
