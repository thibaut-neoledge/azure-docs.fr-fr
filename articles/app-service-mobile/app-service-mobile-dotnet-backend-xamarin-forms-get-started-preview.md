<properties
	pageTitle="Prise en main de Mobile Apps à l’aide de Xamarin.Forms"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Apps pour le développement Xamarin.Forms"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="normesta"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>

#Créer une application Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Vue d'ensemble

Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin.Forms en utilisant un serveur principal d’applications mobiles Azure. Vous allez créer un serveur principal d’applications mobiles et une simple application Xamarin.Forms _Todo list_ qui stocke les données d’application dans Azure.

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Apps pour les applications Xamarin.Forms.

##Conditions préalables

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n’avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).
 
* [Visual Studio Community 2013] ou version ultérieure. Si vous installez Visual Studio Community 2013, installez [Xamarin] séparément. Vous pouvez installer les outils Xamarin quand vous installez Visual Studio 2015.

* Un Mac sur lequel sont installés [Xcode] 7.0 ou version ultérieure et [Xamarin Studio].
 
     >[AZURE.NOTE]Si vous envisagez de créer votre application sur un ordinateur Windows à l’aide de Visual Studio, vous devez quand même avoir accès à un Mac en réseau pour effectuer cette opération.
 
>[AZURE.NOTE]Si vous voulez prendre en main Azure App Service avant de créer un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), où vous pouvez créer immédiatement une première application mobile temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Créer un serveur principal d'applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Télécharger le projet de serveur

1. Sur votre PC, accédez au [portail Azure]. Cliquez sur **Parcourir tout** > **Applications mobiles**, puis cliquez sur le serveur principal d’applications mobiles que vous venez de créer.

2. Dans le panneau de l’application Mobile App, cliquez sur **Paramètres** et sous **Application mobile** cliquez sur **Démarrage rapide** > **Xamarin.Forms**.
 
3. Sous **Télécharger et exécuter votre projet de serveur**, cliquez sur **Télécharger**. Extrayez les fichiers projet compressés sur votre ordinateur et ouvrez la solution dans Visual Studio.
 
## Tester localement votre projet de serveur principal

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publier le projet de serveur sur Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##Télécharger et exécuter la solution Xamarin.Forms

Plusieurs options s’offrent à vous. Vous pouvez télécharger la solution sur un Mac et l’ouvrir dans Xamarin Studio, ou bien télécharger la solution sur un ordinateur Windows et l'ouvrir dans Visual Studio. Vous pouvez également utiliser les deux environnements et basculer entre les deux. Tenez compte du fait que :

* L’exécution du projet iOS de votre solution s’effectue plus facilement sur un Mac. Vous pouvez utiliser Visual Studio sur votre ordinateur Windows si vous le souhaitez, mais c’est un peu plus compliqué, car vous devez vous connecter à un Mac en réseau. Si vous souhaitez effectuer cette opération, consultez la rubrique [Installation de Xamarin.iOS sur Windows].
* Vous pouvez exécuter le projet Android sur votre ordinateur Windows ou bien sur votre Mac.
* Vous ne pouvez exécuter le(s) projet(s) Windows qu’à l'aide de Visual Studio sur un ordinateur Windows.

Puis, procédez comme suit :

 1. Sur votre Mac ou votre ordinateur Windows, ouvrez le [portail Azure] dans une fenêtre de navigateur.
 2. Sous **Télécharger et exécuter votre projet Xamarin.Forms**, cliquez sur le bouton **Télécharger**.

    Cette opération télécharge un projet qui contient une application cliente connectée à votre application mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

 3. Extrayez le projet que vous avez téléchargé, puis ouvrez-le dans Xamarin Studio ou Visual Studio.

	![][9]

	![][8]

###Exécuter le projet iOS

####Dans Xamarin Studio

1. Cliquez avec le bouton droit sur le projet iOS, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **Exécuter**, cliquez sur **Démarrer le débogage** pour créer le projet et démarrer l’application dans l’émulateur iPhone.

####Dans Visual Studio
1. Cliquez avec le bouton droit sur le projet iOS, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **Générer**, cliquez sur **Gestionnaire de configuration**. 
3. Dans la boîte de dialogue **Gestionnaire de configuration**, activez les cases à cocher **Générer** et **Déployer** du projet iOS.
4. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application dans l’émulateur iPhone.

Dans l’application, tapez un texte explicite, tel que _Découvrir Xamarin_, puis cliquez sur le bouton **+**.

![][10]

Ceci envoie une demande POST vers le nouveau backend d'application mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend d'application mobile et les données sont affichées dans la liste.

> [AZURE.NOTE]Vous trouverez le code qui vous permet d’accéder à votre serveur principal d’applications mobiles dans le fichier C#ToDoActivity.cs du projet de bibliothèque de classes portables de votre solution.

###Exécuter le projet Android

####Dans Xamarin Studio

1. Cliquez avec le bouton droit sur le projet Android, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **Exécuter**, cliquez sur **Démarrer le débogage** pour générer le projet et démarrer l’application dans l’émulateur Android.

####Dans Visual Studio
1. Cliquez avec le bouton droit sur le projet Android, puis cliquez sur **Définir comme projet de démarrage**.
4. Dans le menu **Générer**, cliquez sur **Gestionnaire de configuration**. 
5. Dans la boîte de dialogue **Gestionnaire de configuration**, activez les cases à cocher **Générer** et **Déployer** du projet Android.
6. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application dans l’émulateur Android.

Dans l’application, tapez un texte explicite, tel que _Découvrir Xamarin_, puis cliquez sur le bouton **+**.

![][11]

Ceci envoie une demande POST vers le nouveau backend d'application mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend d'application mobile et les données sont affichées dans la liste.

> [AZURE.NOTE]Vous trouverez le code qui vous permet d’accéder à votre serveur principal d’applications mobiles dans le fichier C#ToDoActivity.cs du projet de bibliothèque de classes portables de votre solution.


###Exécuter le projet Windows

####Dans Visual Studio
1. Cliquez avec le bouton droit sur les projets Windows, puis cliquez sur **Définir comme projet de démarrage**.
4. Dans le menu **Générer**, cliquez sur **Gestionnaire de configuration**. 
5. Dans la boîte de dialogue **Gestionnaire de configuration**, activez les cases à cocher **Générer** et **Déployer** du projet Windows que vous avez choisi.
6. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application dans l’émulateur Windows.

Dans l’application, tapez un texte explicite, tel que _Découvrir Xamarin_, puis cliquez sur le bouton **+**.
	
Ceci envoie une demande POST vers le nouveau backend d'application mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend d'application mobile et les données sont affichées dans la liste.

![][12]
	
> [AZURE.NOTE]Vous trouverez le code qui vous permet d’accéder à votre serveur principal d’applications mobiles dans le fichier C#ToDoActivity.cs du projet de bibliothèque de classes portables de votre solution.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[portail Azure]: https://portal.azure.com/
[JavaScript backend version]: ../partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: ../app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: ../app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installation de Xamarin.iOS sur Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
 

<!---HONumber=August15_HO8-->