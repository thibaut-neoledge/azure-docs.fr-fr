<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

Ce didacticiel présente l'ajout d'un service principal cloud à une application Android à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version principale JavaScript][] de cette rubrique.

Voici une capture d'écran de l'application terminée :

![][]

Pour suivre ce didacticiel, vous avez besoin des [Outils de développement Android][], qui incluent l'IDE (environnement de développement intégré) Eclipse, le plug-in ADT (outils de développement Android) et la dernière plateforme Android. Android 4.2 ou une version ultérieure est nécessaire.

Le projet de démarrage rapide téléchargé contient le Kit de développement logiciel (SDK) Mobile Services pour Android. Alors que ce projet requiert Android 4.2 ou une version ultérieure, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne disposez pas d&eacute;j&agrave; d'un compte Azure, vous pouvez obtenir une &eacute;valuation gratuite et 10&nbsp;services mobiles gratuits que vous pouvez utiliser m&ecirc;me apr&egrave;s l'expiration de l'&eacute;valuation. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][]]

## Téléchargement du service mobile sur votre ordinateur local

Maintenant que vous avez créé le service mobile, téléchargez votre projet de service mobile personnalisé afin de l'exécuter sur votre ordinateur local ou sur votre machine virtuelle.

1.  Cliquez sur le service mobile que vous venez de créer, puis dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

    ![][1]

2.  Si vous ne l'avez pas encore fait, téléchargez et installez [Visual Studio Professional 2013][] ou une version ultérieure.

3.  Cliquez sur **Télécharger** sous **Télécharger et publier votre service dans le cloud**.

    Cela permet de télécharger le projet Visual Studio qui implémente votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

4.  Téléchargez également votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez où vous l'enregistrez.

## Test du service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## Publication de votre service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## Création d'une application Android

Dans cette section, vous allez créer une application Android connectée à votre service mobile.

1.  Dans le [portail de gestion][], cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

    ![][2]

3.  Si ce n'est pas déjà fait, téléchargez et installez les [outils de développement Android][Outils de développement Android] sur votre ordinateur local ou sur votre machine virtuelle.

4.  Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**.

    Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre application Android

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1.  Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, puis développez les fichiers sur votre ordinateur.

2.  Dans Eclipse, cliquez sur **Fichier**, sur **Importer**, développez **Android**, puis cliquez sur **Existing Android Code into Workspace** et sur **Suivant.**

    ![][3]

3.  Cliquez sur **Parcourir**, accédez à l'emplacement des fichiers projet développés, cliquez sur **OK**, veillez à ce que le projet TodoActivity soit sélectionné, puis cliquez sur **Terminer**.

    ![][4]

    Cette opération importe les fichiers projet dans l'espace de travail actuel.

    ![][5]

4.  À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet dans l'émulateur Android.

    <div class="dev-callout"><strong>Remarque</strong> <p>Afin d'ex&eacute;cuter le projet dans l'&eacute;mulateur Android, vous devez d&eacute;finir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour cr&eacute;er et g&eacute;rer ces appareils.</p></div>

5.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur **Ajouter**.

    ![][6]

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

    <div class="dev-callout"><strong>Remarque</strong> 
<p>Vous pouvez v&eacute;rifier le code qui se trouve dans le fichier ToDoActivity.java et permet d'acc&eacute;der au service mobile pour ex&eacute;cuter une requ&ecirc;te et ins&eacute;rer des donn&eacute;es.</p>
</div>

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   [Prise en main des données][]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Résolution de problèmes liés à un service principal Mobile Services .NET][]

    En savoir plus sur le diagnostic et la résolution de problèmes liés à un service principal Mobile Services .NET.

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [version principale JavaScript]: /fr-fr/documentation/articles/mobile-services-android-get-started/
  []: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
  [Outils de développement Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [portail de gestion]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
  [3]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
  [4]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png
  [5]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png
  [6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [Résolution de problèmes liés à un service principal Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
