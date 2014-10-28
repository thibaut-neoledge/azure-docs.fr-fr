<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Inscription du package de votre application Windows Store pour l'authentification Microsoft

Azure Mobile Services prend en charge les méthodes d'authentification pilotée par le client et pilotée par le serveur. L'authentification pilotée par le serveur utilise des fournisseurs d'identité, y compris le compte Microsoft. Lorsque vous utilisez un compte Microsoft avec une authentification pilotée par le serveur sans inscrire votre application auprès de Mobile Services, les utilisateurs sont invités à fournir des informations d'identification à chaque demande d'authentification. Lorsque vous inscrivez votre application, les informations d'identification du compte Microsoft sont mises en cache et peuvent être utilisées pour l'authentification sans que l'utilisateur ne soit invité à les fournir à nouveau. Cette rubrique montre comment inscrire le package de votre application Windows Store et ainsi bénéficier d'une expérience de connexion améliorée avec le compte Microsoft lorsque vous utilisez Azure Mobile Services pour l'authentification.

> [WACOM.NOTE]Visual Studio 2013 makes it easy to register your Windows Store app package with Mobile Services. Pour plus d'informations, consultez la page [Démarrage rapide : ajout de notifications Push pour un service mobile][] dans le Centre de développement Windows.

L'authentification gérée par le client peut être employée pour fournir une authentification unique sur un appareil Windows en utilisant Live Connect. Si vous utilisez des API Live Connect, vous n'avez pas besoin de suivre la procédure décrite dans cette rubrique. Pour plus d'informations, consultez la page [Authentification unique de votre application Windows Store avec Live Connect][].

> [WACOM.NOTE]Client-managed authentication using Live Connect is not yet supported in a .NET backend mobile service.

[WACOM.INCLUDE [mobile-services-register-windows-store-app][]]

Après l'inscription du package de votre application, pensez à fournir la valeur **true** pour *useSingleSignOn* lorsque vous appelez la méthode [LoginAsync][]. Vos utilisateurs bénéficient ainsi d'une expérience de connexion améliorée lorsqu'ils utilisent un compte Microsoft.

  [Démarrage rapide : ajout de notifications Push pour un service mobile]: http://go.microsoft.com/fwlink/p/?LinkId=309101
  [Authentification unique de votre application Windows Store avec Live Connect]: /fr-fr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [LoginAsync]: http://go.microsoft.com/fwlink/p/?LinkId=311594
