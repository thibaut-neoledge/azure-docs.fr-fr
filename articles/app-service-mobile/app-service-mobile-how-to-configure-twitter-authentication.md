---
title: Comment configurer l&quot;authentification Twitter pour votre application App Services
description: "Découvrez comment configurer l&quot;authentification Twitter pour votre application App Services."
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ce436f2581780e1aa7cdb95cc59bda9ce6398d4f


---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Comment configurer votre application App Service de manière à utiliser la connexion via Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser Twitter comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d’un compte Twitter avec une adresse électronique et un numéro de téléphone vérifiés. Pour créer un compte Twitter, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Inscription de votre application avec Twitter
1. Connectez-vous au [portail Azure]et accédez à votre application. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application Twitter.
2. Accédez au site web [Twitter Developers] , connectez-vous avec vos identifiants Twitter, puis cliquez sur **Create New App**.
3. Entrez le **Nom** et une **Description** pour votre nouvelle application. Collez l’**URL** de votre application en guise de **Site web**. Ensuite, pour l’**URL de rappel**, collez l’**URL de rappel** que vous avez copiée précédemment. Il s’agit de la passerelle de votre application Mobile App suivie du chemin */.auth/login/twitter/callback*. Par exemple, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Assurez-vous d'utiliser le schéma HTTPS.
4. Au bas de la page, lisez et acceptez les termes du contrat. Ensuite, cliquez sur **Create your Twitter application**. Cette opération inscrit l'application et affiche les détails de la demande.
5. Cliquez sur l’onglet **Paramètres**, activez l’option **Autoriser la connexion à Twitter via cette application**, puis cliquez sur **Mettre à jour les paramètres**.
6. Sélectionnez l’onglet **Keys and Access Tokens** . Prenez note des valeurs de **Clé de consommateur (clé API)** et de **Secret de consommateur (clé secrète API)**.
   
   > [!NOTE]
   > La clé secrète consommateur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.
   > 
   > 

## <a name="secrets"> </a>Ajout des informations Twitter à votre application
1. Revenez au [portail Azure]et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification/Autorisation**.
2. Si la fonctionnalité Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **On**.
3. Cliquez sur **Twitter**. Collez-y les valeurs d'ID d'application et de clé secrète d'application que vous avez obtenues précédemment. Cliquez ensuite sur **OK**.
   
   ![][1]
   
   Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.
4. (Facultatif) Pour restreindre l’accès à votre site aux seuls utilisateurs authentifiés par Twitter, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Twitter**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Twitter pour être authentifiées.
5. Cliquez sur **Save**.

Vous êtes maintenant prêt à utiliser Twitter pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[portail Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md



<!--HONumber=Nov16_HO3-->


