<properties 
	pageTitle="Prise en main de Mobile Services pour les applications Windows Store | Centre de développement mobile" 
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement Windows Store en C# ou JavaScript." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/10/2015" 
	ms.author="glenga"/>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Ce didacticiel présente l'ajout d'un service principal cloud à une application Windows universelle à l'aide d'Azure Mobile Services. Les solutions d'applications Windows universelles incluent des projets pour Windows Store 8.1 et Windows Phone Store 8.1, et un projet partagé commun. Pour plus d'informations, consultez la page [Créer des applications Windows universelles qui ciblent Windows et Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

Dans ce didacticiel, vous allez créer un service mobile et une application simple ( *To do list*) qui stocke les données d'application dans le nouveau service mobile. Le service mobile à créer utilise du code JavaScript pour la logique métier côté serveur. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur dans les langages .NET pris en charge à l'aide de Visual Studio, consultez la version principale .NET de cette rubrique.

>[AZURE.NOTE]Cette rubrique vous présente la création d'un projet de service mobile et d'une application Windows universelle à l'aide du portail de gestion Azure. Visual Studio 2013 vous permet également d'ajouter un nouveau projet de service mobile dans une solution Visual Studio existante. Pour plus d'informations, consultez [Ajouter Mobile Services à une application existante](mobile-services-javascript-backend-windows-universal-dotnet-get-started-data.md).

>Pour ajouter un service mobile à un projet d'application Windows Phone 8.0 ou Windows Phone Store 8.1, consultez la page [Ajouter Mobile Services à une application Windows Phone existante](mobile-services-windows-phone-get-started-data.md).

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* un compte Azure actif. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express pour Windows] 

## Création d'un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Création d'une application Windows universelle

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application Windows universelle ou modifier un projet d'application Windows Store ou Windows Phone existant afin de vous connecter au service mobile. 

Dans cette section, vous allez créer une application Windows universelle connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

   
2. Dans l'onglet de démarrage rapide, cliquez sur **Windows** sous **Choisissez une plateforme** et développez **Créer une application Windows Store**.

   	Cette opération affiche les trois étapes faciles permettant de créer une application Windows Store connectée à votre service mobile.

  	![Mobile Services quickstart steps](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Si ce n'est pas déjà fait, téléchargez et installez [Visual Studio 2013 Express pour Windows] sur votre ordinateur local ou sur votre machine virtuelle.

4. Cliquez sur **Créer une table TodoItem** pour créer une table permettant de stocker les données d'application.

5. Sous **Download and run your app**, sélectionnez une langue pour votre application, puis cliquez sur **Télécharger**. 

  	Cette opération télécharge le projet de votre exemple d'application *To do list* connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement d'enregistrement.

## Exécution de votre application Windows

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]Vous pouvez vérifier le code qui se trouve dans le fichier MainPage.xaml.cs et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

## Étapes suivantes
Vous avez terminé les étapes de démarrage rapide. Découvrez ensuite comment effectuer d'autres tâches importantes dans Mobile Services : 

* [Ajouter Mobile Services à une application existante][Prise en main des données]
  <br/>Découvrez comment stocker et interroger des données à l'aide de Mobile Services.

* [Prise en main de la synchronisation des données hors connexion]
  <br/>En savoir plus sur la synchronisation des données hors connexion pour rendre votre application réactive et robuste.

* [Ajouter l'authentification à votre application Mobile Services ][Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d'identité.

* [Ajout des notifications push à votre application][Prise en main des notifications Push] 
  <br/>Découvrez comment envoyer une notification Push très simple à votre application.

Pour plus d'informations sur les applications Windows universelles, consultez la page [Prise en charge de plusieurs plateformes d'appareil à partir d'un service mobile unique](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs).

<!-- Anchors. -->
[Prise en main de Mobile Services]:#getting-started
[Création d'un service mobile]:#create-new-service
[Définition de l'instance de service mobile]:#define-mobile-service-instance
[Étapes suivantes]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Prise en main des données]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-data.md
[Prise en main des données]: mobile-services-windows-store-dotnet-get-started-data.md
[Prise en main de la synchronisation des données hors connexion]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Prise en main de l'authentification]: mobile-services-windows-store-dotnet-get-started-users.md
[Prise en main des notifications Push]: mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Visual Studio 2013 Express pour Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Kit de développement logiciel (SDK) Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portail de gestion]: https://manage.windowsazure.com/
[Prise en main des données dans Mobile Services avec Visual Studio 2012]: mobile-services-windows-store-dotnet-get-started-data-vs2012.md


<!--HONumber=52-->