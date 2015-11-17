<properties
	pageTitle="Prise en main d'Azure App Service Mobile Apps pour les applications Xamarin.iOS | Microsoft Azure"
	description="Suivez ce didacticiel pour commencer à utiliser Mobile Apps pour le développement Xamarin.iOS."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/03/2015"
	ms.author="normesta"/>


#Création d’une application Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Vue d'ensemble

Ce didacticiel vous montre comment ajouter un service de serveur principal basé sur le cloud à une application mobile Xamarin.iOS à l’aide d’un serveur principal d’application mobile Azure. Vous allez créer un serveur principal d’application mobile et une simple application Xamarin.iOS _Todo list_ qui stocke les données d’application dans Azure.

Le suivi de ce didacticiel est un prérequis pour tous les autres didacticiels Xamarin.iOS sur l’utilisation de la fonctionnalité Mobile Apps dans Azure App Service.

##Configuration requise

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] ou version ultérieure. Si vous installez Visual Studio Community 2013, installez [Xamarin] séparément. Vous pouvez installer les outils Xamarin en même temps que Visual Studio 2015.

* Un Mac sur lequel sont installés [Xcode] 7.0 ou version ultérieure et [Xamarin Studio].

     >[AZURE.NOTE]Si vous envisagez de créer votre application sur un ordinateur Windows à l’aide de Visual Studio, vous devez quand même avoir accès à un Mac en réseau pour effectuer cette opération.

>[AZURE.NOTE]Si vous souhaitez commencer à utiliser Azure App Service avant d’ouvrir un compte Azure, accédez à [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile). Là, vous pouvez créer immédiatement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ni aucun engagement.

## Créer un serveur principal d'applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Télécharger le projet de serveur

1. Sur votre PC, accédez au [portail Azure]. Cliquez sur **Parcourir tout** > **Mobile Apps**, puis cliquez sur le serveur principal d’application mobile que vous venez de créer.

2. Dans le panneau de l’application Mobile App, cliquez sur **Paramètres** et sous **Application mobile** cliquez sur **Démarrage rapide** > **Xamarin.iOS**.

3. Sous **Télécharger et exécuter votre projet de serveur**, cliquez sur **Télécharger**. Extrayez les fichiers projet compressés sur votre ordinateur et ouvrez la solution dans Visual Studio.

## Tester localement votre projet de serveur principal

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]

## Publier le projet de serveur sur Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## Télécharger et exécuter l’application Xamarin.iOS

1. Sur votre Mac, ouvrez le [portail Azure] dans une fenêtre de navigateur.

>[AZURE.NOTE]Il est plus facile d’exécuter votre application Xamarin.iOS sur un Mac. Vous pouvez exécuter l’application Xamarin.iOS à l’aide de Visual Studio sur votre ordinateur Windows si vous le souhaitez, mais c’est un peu plus compliqué, car vous devez vous connecter à un Mac en réseau. Si vous souhaitez effectuer cette opération, consultez [Installation de Xamarin.iOS sur Windows].

2. Sous **Télécharger et exécuter votre projet Xamarin.iOS**, cliquez sur le bouton **Télécharger**.

  	Cette opération télécharge un projet qui contient une application cliente connectée à votre application mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l’emplacement où vous l’avez enregistré.

3. Extrayez le projet que vous avez téléchargé, puis ouvrez-le dans Xamarin Studio (ou Visual Studio).

	![][9]

	![][8]

4. Appuyez sur la touche F5 pour générer le projet et démarrer l’application dans l’émulateur iPhone.

5. Dans l’application, tapez un texte explicite, tel que _Découvrir Xamarin_, puis cliquez sur le bouton **+**.

	![][10]

	Ceci envoie une demande POST vers le nouveau backend d'application mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend d’application mobile et les données sont affichées dans la liste.

>[AZURE.NOTE]Vous pouvez vérifier le code (se trouvant dans le fichier C# QSTodoService.cs) qui accède à votre backend d’application mobile pour exécuter une requête et insérer des données.

##Étapes suivantes

* [Ajouter l’authentification à votre application ](app-service-mobile-xamarin-ios-get-started-users.md) <br/>Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d’identité.

* [Ajouter les notifications push à votre application](app-service-mobile-xamarin-ios-get-started-push.md) <br/>En savoir plus sur l’envoi d’une notification Push très basique à votre application.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[portail Azure]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installation de Xamarin.iOS sur Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/

<!---HONumber=Nov15_HO3-->