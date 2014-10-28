<properties pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel pr&eacute;sente l'ajout d'un service principal cloud &agrave; une application Windows Phone&nbsp;8 &agrave; l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez cr&eacute;er un service mobile et une simple application <em>To do list</em> qui stocke les donn&eacute;es d'application dans le nouveau service mobile.</p>
<p>Si vous pr&eacute;f&eacute;rez regarder une vid&eacute;o, le clip sur la droite suit la m&ecirc;me proc&eacute;dure que ce didacticiel. Dans la vid&eacute;o, apr&egrave;s avoir pr&eacute;sent&eacute; Mobile Services, Nick Harris d&eacute;crit la cr&eacute;ation de votre premier service mobile et explique comment s'y connecter &agrave; partir d'une application Windows Store.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">13:18:00</span></div>

</div>

Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile à créer utilise du code JavaScript pour la logique métier côté serveur. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur dans les langages .NET pris en charge à l'aide de Visual Studio, consultez la [version principale .NET][] de cette rubrique.

Voici une capture d'écran de l'application terminée :

![][]

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][]. Pour créer une application Windows Phone 8.1, Visual Studio 2013 Update 2 (ou une version ultérieure) doit être installé.

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## 

## <span class="short-header">Création d'une application</span>Création d'une application Windows Phone

</h2>
Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Windows Phone 8 connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Windows Phone 8** sous **Choisissez une plateforme** et développez **Créer une application Windows Phone 8**.

    ![][1]

    Cette opération affiche les trois étapes faciles permettant de créer une application Windows Phone connectée à votre service mobile.

    ![][2]

3.  Si ce n'est pas déjà fait, téléchargez et installez [Visual Studio 2012 Express pour Windows Phone][] sur votre ordinateur local.

    > [WACOM.NOTE]Pour créer une application Windows Phone 8.1, Visual Studio 2013 Update 2 doit être installé.

4.  Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.

5.  Sous **Download and run app**, cliquez sur **Télécharger**.

    Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre application Windows Phone

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1.  Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, développez les fichiers sur votre ordinateur et ouvrez le fichier solution dans Visual Studio.

2.  (Facultatif) Si vous créez une application Windows Phone 8.1, cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, cliquez sur **Propriétés**, définissez **Target Windows Phone OS Version** sur **Windows Phone 8.1**, puis cliquez sur **Oui** pour mettre à niveau le projet.

3.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur **Enregistrer**.

    ![][3]

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

    > [WACOM.NOTE]Vous pouvez vérifier le code (se trouvant dans le fichier MainPage.xaml.cs) qui accède au service mobile pour exécuter une requête et insérer des données.

5.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

    ![][4]

    Cela vous permet de parcourir les données insérées par l'application dans la table.

    ![][5]

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   [Prise en main des données][]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de la synchronisation des données hors connexion][]

    En savoir plus sur la synchronisation des données hors connexion pour rendre votre application réactive et robuste.

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [regarder le didacticiel]: http://go.microsoft.com/fwlink/?LinkId=290816
  [version principale .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  []: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2FFfr-fr%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [Visual Studio 2012 Express pour Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [3]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-data/
  [Prise en main de la synchronisation des données hors connexion]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-users/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
