<properties urlDisplayName="Shared Access Signature Part 1" pageTitle="Inscription du package de votre application Windows Store pour l'authentification Microsoft" metaKeywords="" description="Découvrez comment inscrire votre application Windows Store pour l'authentification Microsoft dans votre application Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Inscription du package de votre application Windows Store pour l'authentification Microsoft

Azure Mobile Services prend en charge les méthodes d'authentification pilotée par le client et pilotée par le serveur. L'authentification pilotée par le serveur utilise des fournisseurs d'identité, y compris le compte Microsoft. Lorsque vous utilisez un compte Microsoft avec une authentification pilotée par le serveur sans inscrire votre application auprès de Mobile Services, les utilisateurs sont invités à fournir des informations d'identification à chaque demande d'authentification. Lorsque vous inscrivez votre application, les informations d'identification du compte Microsoft sont mises en cache et peuvent être utilisées pour l'authentification sans que l'utilisateur ne soit invité à les fournir à nouveau. Cette rubrique montre comment inscrire le package de votre application Windows Store et ainsi bénéficier d'une expérience de connexion améliorée avec le compte Microsoft lorsque vous utilisez Azure Mobile Services pour l'authentification. 

>[WACOM.NOTE]Visual Studio 2013 facilite l'inscription du package de votre application Windows Store auprès de Mobile Services. Pour plus d'informations, consultez la rubrique <a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">Démarrage rapide : Ajout de notifications Push à un service mobile</a> dans le Centre de développement Windows.

L'authentification gérée par le client peut être employée pour fournir une authentification unique sur un appareil Windows en utilisant Live Connect. Si vous utilisez des API Live Connect, vous n'avez pas besoin de suivre la procédure décrite dans cette rubrique. Pour plus d'informations, consultez la page [Authentification unique de votre application Windows Store avec Live Connect].   

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Une fois que vous avez inscrit le package de votre application, indiquez la valeur <strong>true</strong> pour le paramètre <em>useSingleSignOn</em> lorsque vous appelez la méthode <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> . Vos utilisateurs bénéficient ainsi d'une expérience de connexion améliorée lorsqu'ils utilisent un compte Microsoft.

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet/
[Authentification unique de vos applications Windows Store avec Live Connect]: /fr-fr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Prise en main des utilisateurs C#]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/
[Prise en main des utilisateurs JavaScript]: /fr-fr/develop/mobile/tutorials/get-started-with-users-js/

<!--HONumber=35.1-->
