<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Prise en main de la synchronisation des données hors connexion dans Mobile Services

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows Store&nbsp;C#">Windows Store&nbsp;C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/fr-fr/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS" class="current">iOS</a>
<a href="/fr-fr/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/fr-fr/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

Ce didacticiel traite de la fonctionnalité de synchronisation hors connexion de Mobile Services, qui permet aux développeurs d'écrire des applications utilisables même lorsque l'utilisateur final n'a pas accès au réseau.

La synchronisation hors connexion possède plusieurs utilisations potentielles :

-   Améliorer la réactivité de l'application en mettant en cache les données de serveur localement sur l'appareil
-   Rendre les applications résistantes à une connectivité réseau intermittente
-   Permettre aux utilisateurs finaux de créer et de modifier des données même en l'absence d'accès au réseau, prenant ainsi en charge des scénarios avec une connectivité faible ou nulle
-   Synchroniser des données sur plusieurs appareils et détecter des conflits lorsque le même enregistrement est modifié par deux appareils

Ce didacticiel va vous montrer comment mettre à jour l'application à partir du didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main de données][Prise en main de données] pour pouvoir prendre en charge les fonctionnalités hors connexion d'Azure Mobile Services. Ensuite, vous allez ajouter les données dans le cadre d'un scénario hors connexion déconnecté, synchroniser ces éléments dans la base de données en ligne, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].

## Obtention de l'exemple d'application

**Un didacticiel complet sera bientôt disponible. En attendant, découvrez l'exemple de [Liste ToDo hors connexion iOS][Liste ToDo hors connexion iOS] et [ce billet de blog][ce billet de blog].**

<!-- ## Next steps  * [How to use the Mobile Services client library for iOS]    -->
<!-- URLs. -->

  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows Store C#"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /fr-fr/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-ios-get-started/
  [Prise en main de données]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data/
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28
  [Liste ToDo hors connexion iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
  [ce billet de blog]: http://aka.ms/iosoffline
