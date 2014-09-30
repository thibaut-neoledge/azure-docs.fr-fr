<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="hero-article" ms.date="08/18/2014" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Ce didacticiel présente l'ajout d'un service principal cloud à une application Windows universelle à l'aide d'Azure Mobile Services.

Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile à créer utilise du code JavaScript pour la logique métier côté serveur. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur dans les langages .NET pris en charge à l'aide de Visual Studio, consultez la version principale .NET de cette rubrique.

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-   Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
-   [Visual Studio 2013 Express pour Windows][Visual Studio 2013 Express for Windows]

## Création d'un service mobile

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Création d'une application Windows universelle

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application Windows universelle ou modifier un projet d'application Windows Store ou Windows Phone existant afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Windows universelle connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Windows** sous **Choisissez une plateforme** et développez **Créer une application Windows Store**.

   	![](./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png)

    Cette opération affiche les trois étapes faciles permettant de créer une application Windows Store connectée à votre service mobile.

  	![](./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png)

3.  Si ce n'est déjà fait, téléchargez et installez [Visual Studio 2013][Visual Studio 2013 Express for Windows] sur votre ordinateur local ou sur votre machine virtuelle.

4.  Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.

5.  Sous **Download and run your app**, sélectionnez une langue pour votre application, puis cliquez sur **Télécharger**.

    Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre application Windows

[WACOM.INCLUDE [mobile-services-javascript-backend-run-app](../includes/mobile-services-javascript-backend-run-app.md)]

> [WACOM.NOTE] Dans le fichier default.js, vous pouvez vérifier le code qui permet d'accéder à votre service mobile pour exécuter une requête et insérer des données.

## Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   [Prise en main des données][Get started with data]
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification][Get started with authentication]
    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   [Prise en main des notifications Push][Get started with push notifications]
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

Pour plus d'informations sur les applications Windows universelles, consultez la page [Prise en charge de plusieurs plateformes d'appareil à partir d'un service mobile unique](/en-us/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with data]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Management Portal]: https://manage.windowsazure.com/
