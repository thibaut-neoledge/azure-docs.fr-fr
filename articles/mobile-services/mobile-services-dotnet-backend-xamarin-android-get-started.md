---
title: Prise en main de Mobile Services pour les applications Xamarin Android | Microsoft Docs
description: Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement Xamarin Android.
services: mobile-services
documentationcenter: xamarin
author: lindydonna
manager: dwrede
editor: mollybos

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 07/21/2016
ms.author: donnam

---
# <a name="getting-started"> </a>Prise en main de Mobile Services
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Pour la version Mobile Apps équivalente de cette rubrique, consultez l’article [Créer une application Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md).
> 
> 

Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin Android en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version du backend JavaScript] de cette rubrique.

> [!NOTE]
> Cette rubrique montre comment créer un projet de service mobile à l’aide du portail Azure Classic. Visual Studio 2013 Update 2 vous permet également d'ajouter un nouveau projet de service mobile dans une solution Visual Studio existante. Pour plus d’informations, consultez la page [Démarrage rapide : ajouter un service mobile (.NET principal)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)
> 
> 

Voici une capture d'écran de l'application terminée :

![][0]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Services pour les applications Xamarin Android.

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started). Ce didacticiel requiert [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546). Une version d’évaluation gratuite est disponible.
> 
> 

## Création d’un service mobile
[!INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Création d'une application Xamarin Android
Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail classique pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez télécharger une nouvelle application Xamarin Android et un projet de service pour votre service mobile.

1. Si vous ne l'avez pas déjà fait, installez Visual Studio avec Xamarin. Pour obtenir des instructions, consultez la rubrique [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). Vous pouvez également utiliser Xamarin Studio sur un ordinateur Mac OS X. Consultez la rubrique [Configuration, installation et vérifications pour les utilisateurs Mac](https://msdn.microsoft.com/library/mt488770.aspx).
2. Dans le [portail Classic], cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.
3. Dans l'onglet de démarrage rapide, cliquez sur **Xamarin** sous **Choisissez une plateforme** et développez **Créer une application Xamarin**.
   
       ![][6]
   
       Cette opération affiche les trois étapes faciles permettant de créer une application Xamarin Android connectée à votre service mobile.
   
      ![][7]
4. Sous **Télécharger et publier votre service dans le cloud**, sélectionnez **Android** et cliquez sur **Télécharger**.
   
      Cela permet de télécharger une solution contenant les projets à la fois pour le service mobile et l’exemple d’application *liste des tâches* connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.
5. Téléchargez votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez où vous l'enregistrez.

## Test du service mobile
[!INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publication de votre service mobile
[!INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Exécution de l'application Xamarin Android
La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Accédez au projet client dans la solution de service mobile, dans Visual Studio ou Xamarin Studio.
2. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application. Vous êtes invité à sélectionner un émulateur ou un périphérique USB connecté.
   
   > [!NOTE]
   > Afin d'exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.
   > 
   > 
3. Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur l'icône plus (**+**).
   
    ![][10]
   
    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.
   
   > [!NOTE]
   > Vous pouvez vérifier le code qui se trouve dans le fichier ToDoActivity.cs C# et permet d'accéder au service mobile pour exécuter une requête et insérer des données.
   > 
   > 

## Étapes suivantes
Vous avez terminé les étapes de démarrage rapide. Découvrez ensuite comment effectuer d’autres tâches importantes dans Mobile Services :

* [Prise en main de la synchronisation des données hors connexion] <br/>Découvrez comment le démarrage rapide utilise la synchronisation des données hors connexion afin de rendre l'application réactive et robuste.
* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.
* [Prise en main des notifications Push] <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.
* [Résolution des problèmes d'un backend .NET Mobile Services ] <br/>Découvrez comment diagnostiquer et résoudre les problèmes qui peuvent se produire avec un backend .NET Mobile Services.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Prise en main de la synchronisation des données hors connexion]: mobile-services-xamarin-android-get-started-offline-data.md
[Prise en main de l'authentification]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[Prise en main des notifications Push]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Azure classic portal]: https://manage.windowsazure.com/
[portail Classic]: https://manage.windowsazure.com/
[version du backend JavaScript]: mobile-services-android-get-started.md
[Résolution des problèmes d'un backend .NET Mobile Services ]: mobile-services-dotnet-backend-how-to-troubleshoot.md

<!---HONumber=AcomDC_0727_2016-->