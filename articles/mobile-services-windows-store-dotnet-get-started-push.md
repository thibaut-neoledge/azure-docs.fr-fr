<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />

# Prise en main des notifications Push dans Mobile Services (Push hérité)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store&nbsp;C#" class="current">Windows Store&nbsp;C#</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/fr-fr/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/fr-fr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title=".NET backend">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

Cette rubrique vous explique comment, via Visual Studio 2013, utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Windows Store. Le didacticiel vous apprend à ajouter des notifications Push au projet de démarrage rapide à l'aide du Service de notifications Windows Push (WNS), directement à partir de Visual Studio. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

> [WACOM.NOTE]Cette rubrique prend en charge les services mobiles *existants* qui n'ont *pas encore été mis à niveau* pour utiliser l'intégration de Notification Hubs. Lorsque vous créez un *nouveau* service mobile, cette fonctionnalité intégrée est automatiquement activée. Pour les nouveaux services mobiles, consultez la rubrique [Prise en main des notifications Push][Prise en main des notifications Push].
>
> Mobile Services s'intègre à Azure Notification Hubs pour prendre en charge une fonctionnalité de notification Push supplémentaire, comme les modèles, les plateformes multiples et la mise à l'échelle améliorée. *Vous devez mettre à niveau vos services mobiles existants pour utiliser Notification Hubs lorsque cela est possible*. Une fois la mise à niveau effectuée, consultez cette version de la [Prise en main des notifications Push][Prise en main des notifications Push].

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application pour les notifications Push et configuration de Mobile Services][Inscription de votre application pour les notifications Push et configuration de Mobile Services]
2.  [Mise à jour du code de notification Push][Mise à jour du code de notification Push]
3.  [Insertion de données pour recevoir des notifications][Insertion de données pour recevoir des notifications]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données] pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l’Assistant Ajouter une notification Push crée cette connexion pour vous.

## <a name="register"></a><span class="short-header">Inscription de votre application</span> Ajout et configuration des notifications Push dans l'application

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Développez **services**, **mobile services**, votre nom de service, ouvrez le fichier de code généré, puis contrôlez la méthode **UploadChannel** qui récupère l'ID d'installation et le canal de l'appareil, et insère ces données dans la nouvelle table de canaux.

    L'Assistant a également ajouté l'appel de cette méthode au gestionnaire d'événements **OnLaunched** dans le fichier de code App.xaml.cs. Ainsi, une tentative d'inscription de l'appareil est effectuée chaque fois que l'application est lancée.

2.  Dans l'Explorateur de serveurs, développez **Azure**, **Mobile Services**, votre nom de service et **channels**, puis ouvrez le fichier insert.js.

    Ce fichier, qui est stocké dans votre service mobile, contient du code JavaScript qui est exécuté lorsqu'un client envoie une demande d'inscription d'un appareil en insérant les données dans la table de canaux.

    <div class="dev-callout"><b>Remarque</b>
    <p>La version initiale de ce fichier contient du code qui v&eacute;rifie si l'appareil en question est d&eacute;j&agrave; inscrit. Elle inclut une autre portion du code qui envoie une notification Push lorsqu'une nouvelle inscription est ajout&eacute;e &agrave; la table de canaux. Le code qui envoie une notification Push peut &ecirc;tre inclus dans tout fichier script inscrit. L'emplacement de ce script d&eacute;pend du mode de d&eacute;clenchement de la notification. Les scripts peuvent &ecirc;tre enregistr&eacute;s pour une op&eacute;ration sur une table de type insertion, mise &agrave; jour, suppression ou lecture (t&acirc;che planifi&eacute;e ou API personnalis&eacute;e). Pour plus d'informations, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">Utilisation des scripts serveur dans Mobile Services</a>.</p>
</div>

3.  Appuyez sur la touche F5 pour exécuter l'application et vérifiez que vous recevez immédiatement une notification du service mobile.

    Cette notification, générée par l'insertion d'une ligne dans la nouvelle table de canaux, correspond à l'inscription de l'appareil.

Le code généré permet de démontrer facilement l'émission d'une notification lorsque l'application est exécutée. En pratique, ce scénario n'est généralement pas significatif. Vous supprimez ensuite le code de notification de la table de canaux et le remplacez, sous une forme modifiée, dans la table TodoItem.

## <a name="update-scripts"></a><span class="short-header">Mise à jour du code</span> Mise à jour du code de notification Push généré

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

## <a name="test"></a><span class="short-header">Test de l'application</span>Test des notifications Push dans l'application

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][0]

    Notez qu'une fois l'insertion terminée, l'application reçoit une notification Push de WNS.

    ![][1]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Prise en main de Notification Hubs][Prise en main de Notification Hubs]

    En savoir plus sur l'utilisation de Notification Hubs dans votre application Windows Store.

-   [Envoi de notifications aux abonnés][Envoi de notifications aux abonnés]

    En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

-   [Envoi de notifications aux utilisateurs][Envoi de notifications aux utilisateurs]

    En savoir plus sur l'envoi de notifications Push depuis un service mobile à certains utilisateurs sur n'importe quel appareil.

-   [Envoi de notifications interplateforme aux utilisateurs][Envoi de notifications interplateforme aux utilisateurs]

    En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données][Prise en main des données]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services][Référence de script serveur Mobile Services]

    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services .NET][Guide de fonctionnement Mobile Services .NET]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Inscription de votre application pour les notifications Push et configuration de Mobile Services]: #register
  [Mise à jour du code de notification Push]: #update-scripts
  [Insertion de données pour recevoir des notifications]: #test
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet/
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-create-new-push-vs2013-2]: ../includes/mobile-services-create-new-push-vs2013-2.md
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png
  [Prise en main de Notification Hubs]: /fr-fr/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Envoi de notifications aux abonnés]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet/
  [Envoi de notifications aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users/
  [Envoi de notifications interplateforme aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library/
