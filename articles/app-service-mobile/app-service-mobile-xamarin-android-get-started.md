---
title: Prise en main d'Azure Mobile Apps pour les applications Xamarin.Android
description: Suivez ce didacticiel pour commencer à utiliser Azure Mobile Apps pour le développement d'applications Xamarin Android.
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: erikre
editor: ''

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha

---
# Création d'une application Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Vue d'ensemble
Ce didacticiel montre comment ajouter un service backend cloud à une application Xamarin Android. Pour plus d’informations, consultez [Que sont les applications Mobile Apps ?](app-service-mobile-value-prop.md).

Voici une capture d’écran de l’application terminée :

![][0]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Apps pour les applications Xamarin.Android.

## Conditions préalables
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n’avez pas de compte, vous pouvez vous inscrire pour obtenir une version d’évaluation Azure et jusqu’à 10 applications Mobile App gratuites que vous pourrez conserver après l’expiration de votre période d’évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio avec Xamarin. Pour obtenir des instructions, consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

> [!NOTE]
> Si vous voulez prendre en main Azure App Service avant de créer un compte Azure, accédez à la page [Essayer App Service](https://tryappservice.azure.com/?appServiceName=mobile), où vous pouvez créer immédiatement une première application mobile temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

## Créer un serveur principal d'applications mobiles Azure
Suivez ces étapes pour créer un serveur principal d’application mobile.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant configuré un serveur principal d’application mobile Azure qui peut être utilisé par vos applications clientes mobiles. Vous allez ensuite télécharger un projet de serveur pour un serveur principal « todo list » simple et le publier dans Azure.

## Configurer le projet de serveur
[!INCLUDE [app-service-mobile-Configure-New-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Télécharger et exécuter l'application Xamarin.Android
1. Sous **Télécharger et exécuter votre projet Xamarin.Android**, cliquez sur le bouton **Télécharger**.
   
      Cette opération télécharge un projet qui contient une application cliente connectée à votre application mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l’emplacement où vous l’avez enregistré.
2. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application.
3. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur l’icône **Ajouter**.
   
    ![][10]
   
    Ceci envoie une demande POST vers le nouveau backend d'application mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le serveur principal d'applications mobiles et les données apparaissent dans la liste.
   
   > [!NOTE]
   > Vous pouvez vérifier le code qui se trouve dans le fichier C# ToDoActivity.cs et permet d'accéder à votre backend d'application mobile pour exécuter une requête et insérer des données.
   > 
   > 

## Étapes suivantes
* [Ajouter l’authentification à votre application](app-service-mobile-xamarin-android-get-started-users.md) Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d’identité.
* [Ajouter des notifications Push à votre application](app-service-mobile-xamarin-android-get-started-push.md) Découvrez comment ajouter des notifications Push à votre application.
* [Comment utiliser le client géré pour Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md) Découvrez comment utiliser le Kit de développement logiciel (SDK) du client géré dans votre application Xamarin.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1005_2016-->