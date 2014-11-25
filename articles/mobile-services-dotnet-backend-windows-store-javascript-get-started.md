<properties linkid="develop-mobile-tutorials-get-started" urlDisplayName="Get Started" pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C#, VB, or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Ce didacticiel présente l'ajout d'un service principal cloud à une application Windows universelle à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* en HTML et JavaScript qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la version principale JavaScript de cette rubrique.

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-   Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]. Une version d'évaluation gratuite est disponible.

## Création d'un service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Création d'une application Windows universelle

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Windows universelle connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Windows** sous **Choisissez une plateforme** et développez **Créer une application Windows Store**.

    ![][0]

    Cette opération affiche les trois étapes faciles permettant de créer une application Windows Store connectée à votre service mobile.

    ![][1]

3.  Si ce n'est déjà fait, téléchargez et installez [Visual Studio Professional 2013][Visual Studio Professional 2013] sur votre ordinateur local ou sur votre machine virtuelle.

4.  Sous **Télécharger et exécuter votre application et votre service en local**, sélectionnez la langue de votre application Windows Store, puis cliquez sur **Télécharger**.

    Ceci entraîne le téléchargement d'une solution contenant les projets du service mobile et de l'exemple d'application *To do list* connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Test de l'application avec le service mobile local

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

> [WACOM.NOTE] Dans le fichier default.js, vous pouvez vérifier le code qui permet d'accéder à votre service mobile pour exécuter une requête et insérer des données.

## Publication de votre service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  Dans le projet de code partagé, ouvrez le fichier default.js, recherchez le code de création d'une instance [WindowsAzure.MobileServiceClient][WindowsAzure.MobileServiceClient] placez en commentaire le code de création de ce client à l'aide de *localhost* et annulez le commentaire du code de création du client à l'aide de l'URL du service mobile distant, lequel est semblable à ce qui suit :

        var client = new WindowsAzure.MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXX-APPLICATION-KEY-XXXXXX"
                );

    Le client a désormais accès au service mobile publié sur Azure.

2.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

3.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure.

4.  (Facultatif) Dans une solution Windows universelle, remplacez le projet de démarrage par défaut par l'autre application et appuyez de nouveau sur **F5**.

    Notez que les données enregistrées à l'étape précédente sont chargées à partir du service mobile après le démarrage de l'application.

## Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   [Prise en main des données][Prise en main des données]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   [Prise en main des notifications Push][Prise en main des notifications Push]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

Pour plus d'informations sur les applications Windows universelles, consultez la page [Prise en charge de plusieurs plateformes d'appareil à partir d'un service mobile unique][Prise en charge de plusieurs plateformes d'appareil à partir d'un service mobile unique].

  [Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
  [WindowsAzure.MobileServiceClient]: http://msdn.microsoft.com/library/azure/jj554219.aspx
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Prise en charge de plusieurs plateformes d'appareil à partir d'un service mobile unique]: /fr-fr/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
