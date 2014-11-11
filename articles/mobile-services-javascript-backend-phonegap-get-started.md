<properties pageTitle="Get started with Mobile Services with PhoneGap | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for PhoneGap development for iOS, Android, and Windows Phone." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-phonegap" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

# <a name="getting-started"> </a>Prise en main de Mobile Services

Ce didacticiel vous montre comment ajouter un service principal cloud à une application en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile.

Voici une capture d'écran de l'application terminée :

![][0]

### <a name="additional-requirements"></a>Conditions supplémentaires

Pour suivre ce didacticiel, vous devez disposer des outils PhoneGap (v3.2+ requise pour les projets Windows Phone 8).

PhoneGap prend en charge le développement de plusieurs plateformes. Outre les outils PhoneGap, vous devez installer les outils de chacune des plateformes requises :

-   Windows Phone : installez [Visual Studio 2012 Express pour Windows Phone.][Visual Studio 2012 Express pour Windows Phone.]
-   iOS : installez [Xcode][Xcode] (v4.4+ requise).
-   Android : installez les [outils de développement Android][outils de développement Android]
    (le Kit de développement logiciel (SDK) Mobile Services pour Android prend en charge les applications destinées à Android 2.2 (et versions ultérieures). Android 4.2 (ou version ultérieure) est requis pour exécuter l'application de démarrage rapide).

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <span class="short-header">Création d'une application </span>Création d'une application PhoneGap

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application PhoneGap connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **PhoneGap** sous **Choisissez une plateforme** et développez **Créer une application PhoneGap**.

    ![][1]

    Cette opération affiche les trois étapes faciles permettant de créer une application PhoneGap connectée à votre service mobile.

    ![][2]

3.  Si ce n'est déjà fait, téléchargez et installez PhoneGap et au moins un des outils de développement de plateforme (Windows Phone, iOS ou Android).

4.  Cliquez sur **Create TodoItems table** pour créer une table permettant de stocker les données d'application.

5.  Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**.

    Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile, ainsi que le Kit de développement logiciel (SDK) Mobile Services JavaScript. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre nouvelle application PhoneGap

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1.  Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, puis développez les fichiers sur votre ordinateur.

2.  Ouvrez et exécutez le projet conformément aux instructions disponibles sous chaque plateforme.

    -   **Windows Phone 8**

    1.  Windows Phone 8 : ouvrez le fichier .sln situé dans le dossier **platforms\\wp8** de Visual Studio 2012 Express pour Windows Phone.

    2.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

        ![][3]

    -   **iOS**

    1.  Ouvrez le projet du dossier **platforms/ios** dans Xcode.

    2.  Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans l'émulateur iPhone (configuration par défaut pour ce projet).

        ![][0]

    -   **Android**

        1.  Dans Eclipse, cliquez sur **Fichier**, sur **Importer**, développez **Android**, puis cliquez sur **Existing Android Code into Workspace** et sur **Suivant.**

        2.  Cliquez sur **Parcourir**, accédez à l'emplacement des fichiers projet développés, cliquez sur **OK**, veillez à ce que le projet TodoActivity soit sélectionné, puis cliquez sur **Terminer**.

            Cette opération importe les fichiers projet dans l'espace de travail actuel.

            </p>
        3.  À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet dans l'émulateur Android.

            ![][4]

        > [WACOM.NOTE] Pour exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.

3.  Après avoir lancé l'application dans un des émulateurs mobiles ci-dessus, tapez du texte dans la zone de texte, puis cliquez sur **Ajouter**.

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table **TodoItem**. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

    <div class="dev-callout"><strong>Important</strong> <p>Les modifications apport&eacute;es &agrave; ce projet de plateforme seront &eacute;cras&eacute;es si le projet principal est r&eacute;g&eacute;n&eacute;r&eacute; &agrave; l'aide des outils PhoneGap. Par cons&eacute;quent, il est imp&eacute;ratif d'apporter ces modifications dans le r&eacute;pertoire www racine du projet, comme d&eacute;crit &agrave; la section suivante.</p></div>

4.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

    ![][5]

    Cela vous permet de parcourir les données insérées par l'application dans la table.

    ![][6]

## Mise à jour de l'application et régénération des projets pour chaque plateforme

1.  Modifiez les fichiers de code du répertoire « www ». Il s'agit ici de « todolist/www ».

2.  Vérifiez que les outils de toutes les plateformes cibles sont accessibles dans le chemin d'accès système.

3.  Ouvrez une invite de commandes dans le répertoire projet racine, puis exécutez une des commandes spécifiques à la plateforme :

    -   **Windows Phone**

        Exécutez la commande suivante depuis une invite de commandes de développement Visual Studio :

            phonegap local build wp8

    -   **iOS**

        Ouvrez le terminal et exécutez la commande suivante :

            phonegap local build ios

    -   **Android**

        Ouvrez une invite de commandes ou une fenêtre terminal et exécutez la commande suivante :

            phonegap local build android

4.  Ouvrez chaque projet dans l'environnement de développement approprié, comme décrit à la section précédente.

> [WACOM.NOTE] Dans le fichier js/index.js, vous pouvez vérifier le code qui permet d'accéder à votre service mobile pour exécuter une requête et insérer des données.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   [Prise en main des données][Prise en main des données]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [0]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
  [Visual Studio 2012 Express pour Windows Phone.]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [outils de développement Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
  [2]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
  [3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
  [4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png
  [5]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-html-get-started-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-html-get-started-users
