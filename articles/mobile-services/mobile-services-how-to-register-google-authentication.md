---
title: Inscription pour l'authentification Google | Microsoft Docs
description: Découvrez comment inscrire vos applications afin d'utiliser Google pour l'authentification auprès d'Azure Mobile Services.
services: mobile-services
documentationcenter: android
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: multiple
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Inscription des applications pour la connexion Google avec Mobile Services
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[!INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Cette rubrique montre comment inscrire vos applications afin d'utiliser Google pour l'authentification auprès d'Azure Mobile Services.

> [!NOTE]
> Ce didacticiel concerne [Azure Mobile Services](https://azure.microsoft.com/services/mobile-services/), une solution conçue pour vous aider à créer des applications mobiles évolutives pour n'importe quelle plateforme. Grâce à Mobile Services, synchronisez des données, authentifiez des utilisateurs et envoyer des notifications Push en toute simplicité. Cette page est consacrée au didacticiel [Prise en main de l'authentification](mobile-services-ios-get-started-users.md), qui montre comment connecter des utilisateurs à votre application. <br/>Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services](mobile-services-ios-get-started.md).
> 
> 

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Google avec une adresse de messagerie vérifiée. Pour créer un compte Google, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Rendez-vous sur le site Web [API Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), connectez-vous avec votre compte Google, cliquez sur **Créer un projet**, saisissez un **Nom de projet**, puis cliquez sur **Créer**.
2. Dans le menu déroulant **Produits et services**, cliquez sur **Gestionnaires d’API**, puis sous **API sociales**, cliquez sur **API Google+** > **Activer l’API**.
3. Cliquez sur **Informations d’identification** > **Écran de consentement OAuth**, puis sélectionnez votre **Adresse de messagerie**, entrez un **Nom de produit** et cliquez sur **Enregistrer**.
4. Dans l'onglet **Informations d'identification**, cliquez sur **Ajouter des informations d'identification** > **ID client OAuth 2.0**, puis sélectionnez **Application Web**.
5. Entrez l’URL de votre service mobile dans **Origines JavaScript autorisées**, remplacez l’URL générée dans **URI de redirection autorisée** par l’un des formats d’URL suivants, puis cliquez sur **Créer** :

    + **Backend .NET** : `https://<mobile_service>.azure-mobile.net/signin-google`
    + **Service principal JavaScript** : `https://<mobile_service>.azure-mobile.net/login/google`

     >[AZURE.NOTE]Assurez-vous d’utiliser le format de chemin d’accès d’URL de redirection correct pour votre type de serveur principal Mobile Services. Si ce n’est pas le cas, l’authentification échouera.

1. Sur l'écran suivant, notez les valeurs de l'ID du client et de la clé secrète client.
   
   > [!IMPORTANT]
   > La clé secrète client est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.
   > 
   > 

Vous êtes désormais prêt à configurer votre service mobile pour utiliser l'authentification Google dans votre application.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_0727_2016-->