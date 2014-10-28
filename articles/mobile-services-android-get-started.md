<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel pr&eacute;sente l'ajout d'un service principal cloud &agrave; une application Android &agrave; l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez cr&eacute;er un service mobile et une simple application <em>To do list</em> qui stocke les donn&eacute;es d'application dans le nouveau service mobile.</p>
<p>Voici une capture d'&eacute;cran de l'application termin&eacute;e&nbsp;:</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a><span class="time">07:26:00</span></div>

</div>

![][]

Pour suivre ce didacticiel, vous avez besoin des [Outils de développement Android][], qui incluent l'IDE (environnement de développement intégré) Eclipse, le plug-in ADT (outils de développement Android) et la dernière plateforme Android. Android 4.2 ou une version ultérieure est nécessaire.

Le projet de démarrage rapide téléchargé contient le Kit de développement logiciel (SDK) Mobile Services pour Android. Alors que ce projet requiert Android 4.2 ou une version ultérieure, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-create-new-service][]] 

## <h2><span class="short-header">Création d'une application </span>Création d'une nouvelle application Android</h2>

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Android connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

    ![][1]

    Cette opération affiche les trois étapes faciles permettant de créer une application Android connectée à votre service mobile.

    ![][2]

3.  Si ce n'est pas déjà fait, téléchargez et installez les [outils de développement Android][Outils de développement Android] sur votre ordinateur local ou sur votre machine virtuelle.

4.  Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.

5.  Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**.

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

6.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

    ![][7]

    Cela vous permet de parcourir les données insérées par l'application dans la table.

    ![][8]

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   [Prise en main des données][]
   
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification][]
  
    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.
 
-   [Prise en main des notifications Push][]
  
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.


  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [regarder le didacticiel]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services
  []: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
  [Outils de développement Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
  [2]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
  [3]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
  [4]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png
  [5]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
  [6]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
  [7]: ./media/mobile-services-android-get-started/mobile-data-tab.png
  [8]: ./media/mobile-services-android-get-started/mobile-data-browse.png
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-android-get-started-data/
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-android-get-started-users/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
