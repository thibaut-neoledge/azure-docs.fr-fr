<properties 
	pageTitle="Prise en main des notifications Push (Windows Store) | Centre de développement mobile" 
	description="Découvrez comment utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Windows Store (push hérité)." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/11/2014" 
	ms.author="glenga"/>

# Ajout de notifications push à votre application Mobile Services (transmission de type push héritée)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" class="current">Windows Store C#</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/fr-fr/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/fr-fr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title=".NET backend">.NET principal</a> | <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="JavaScript backend" class="current">JavaScript principal</a></div>	

Cette rubrique vous explique comment, via Visual Studio 2013, utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Windows Store. Le didacticiel vous apprend à ajouter des notifications Push au projet de démarrage rapide à l'aide du Service de notifications Windows Push (WNS), directement à partir de Visual Studio. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

>[AZURE.NOTE]Cette rubrique prend en charge les services mobiles <em>existants</em> qui n'<em>ont pas encore été mis à niveau</em> pour utiliser l'intégration à Notification Hubs. Lorsque vous créez un <em>service mobile</em>, cette fonctionnalité intégrée est automatiquement activée. Pour les nouveaux services mobiles, consultez la rubrique [Prise en main des notifications Push)](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md).
>
>Mobile Services s'intègre à Azure Notification Hubs pour prendre en charge une fonctionnalité de notification Push supplémentaire, comme les modèles, les plateformes multiples et la mise à l'échelle améliorée. <em>Vous devez mettre à niveau vos services mobiles existants pour utiliser Notification Hubs lorsque cela est possible</em>. Une fois la mise à niveau effectuée, consultez cette version de la [Prise en main des notifications Push](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md).

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Inscription de votre application pour les notifications Push et configuration de Mobile Services]
2. [Mise à jour du code de notification Push]
3. [Insertion de données pour recevoir des notifications]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services] ou [Prise en main des données] pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l'Assistant Ajouter une notification Push crée cette connexion pour vous. 

<h2><a name="register"></a>Ajout et configuration des notifications Push dans l'application</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Développez <strong>services</strong>, <strong>mobile services</strong>, votre nom de service, ouvrez le fichier de code généré, puis contrôlez la méthode <strong>UploadChannel</strong> qui récupère l'ID d'installation et le canal de l'appareil, et insère ces données dans la nouvelle table de canaux.</p> 

<p>L'Assistant a également ajouté l'appel de cette méthode au gestionnaire d'événements <strong>OnLaunched</strong> dans le fichier de code App.xaml.cs. Ainsi, une tentative d'inscription de l'appareil est effectuée chaque fois que l'application est lancée.</p></li> 
<li><p>Dans l'Explorateur de serveurs, développez <strong>Azure</strong>, <strong>Mobile Services</strong>, votre nom de service et <strong>channels</strong>, puis ouvrez le fichier insert.js.</p> 

<p>Ce fichier, qui est stocké dans votre service mobile, contient du code JavaScript qui est exécuté lorsqu'un client envoie une demande d'inscription d'un appareil en insérant les données dans la table de canaux.</p> 

> [AZURE.NOTE] La version initiale de ce fichier contient du code qui vérifie si l'appareil en question est déjà inscrit. Elle inclut une autre portion du code qui envoie une notification Push lorsqu'une nouvelle inscription est ajoutée à la table de canaux. Le code qui envoie une notification Push peut être inclus dans tout fichier script inscrit. L'emplacement de ce script dépend du mode de déclenchement de la notification. Les scripts peuvent être enregistrés pour une opération sur une table de type insertion, mise à jour, suppression ou lecture (tâche planifiée ou API personnalisée). Pour plus d'informations, consultez la page [Utilisation des scripts serveur dans Mobile Services](http://go.microsoft.com/fwlink/p/?LinkID=287178).

</li> 
<li><p>Appuyez sur la touche F5 pour exécuter l'application et vérifiez que vous recevez immédiatement une notification du service mobile.</p>
<p>Cette notification, générée par l'insertion d'une ligne dans la nouvelle table de canaux, correspond à l'inscription de l'appareil.</p>
</li>
</ol>
Le code généré permet de démontrer facilement l'émission d'une notification lorsque l'application est exécutée. En pratique, ce scénario n'est généralement pas significatif. Vous supprimez ensuite le code de notification de la table de canaux et le remplacez, sous une forme modifiée, dans la table TodoItem. 

<h2><a name="update-scripts"></a>Mise à jour du code de notification Push</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>Test des notifications Push dans votre application</h2>

1. Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2. Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

   	![][13]

   	Notez qu'une fois l'insertion terminée, l'application reçoit une notification Push de WNS.

   	![][14]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

+ [Prise en main de Notification Hubs]
  <br/>En savoir plus sur l'utilisation de Notification Hubs dans votre application Windows Store.

+ [Envoi de notifications aux abonnés]
	<br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

+ [Envoi de notifications aux utilisateurs]
	<br/>En savoir plus sur l'envoi de notifications Push depuis un service mobile à certains utilisateurs sur n'importe quel appareil.

+ [Envoi de notifications Push aux utilisateurs authentifiés]
	<br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Pour plus d'informations sur les rubriques Mobile Services suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur l'inscription et l'utilisation des scripts serveur.

* [Guide de fonctionnement Mobile Services .NET]
  <br/>Découvrez plus en détail comment utiliser Mobile Services avec .NET.

<!-- Anchors. -->
[Inscription de votre application pour les notifications Push et configuration de Mobile Services]: #register
[Mise à jour du code de notification Push]: #update-scripts
[Insertion de données pour recevoir des notifications]: #test
[Étapes suivantes]:#next-steps

<!-- Images. -->











[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet/
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet
[Notifications Push pour les utilisateurs de l'application]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-dotnet
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript et HTML]: /fr-fr/develop/mobile/tutorials/get-started-with-push-js

[Portail de gestion Azure]: https://manage.windowsazure.com/
[objet wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library/
[Validation et modification de données à l'aide de scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet
[Prise en main de Notification Hubs]: /fr-fr/manage/services/notification-hubs/getting-started-windows-dotnet/
[Présentation de Notification Hubs]: /fr-fr/develop/net/how-to-guides/service-bus-notification-hubs/
[Envoi de notifications aux abonnés]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet/
[Envoi de notifications aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users/
[Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293



<!--HONumber=42-->
