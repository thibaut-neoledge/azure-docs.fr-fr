<properties pageTitle="Prise en main des données (iOS) | Centre de développement mobile" description="Découvrez comment utiliser Mobile Services pour tirer parti des données dans votre application iOS." services="" documentationCenter="ios" authors="krisragh" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# Ajout de Mobile Services à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application iOS. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

> [AZURE.NOTE] Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données à partir d'une application iOS à l'aide d'Azure. À cet effet, il vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services](/fr-fr/develop/mobile/tutorials/get-started-ios).

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Téléchargement du projet d'application iOS]
2. [Création du service mobile]
3. [Ajout d'une table de données pour le stockage]
4. [Mise à jour de l'application pour utiliser Mobile Services]
5. [Test de l'application par rapport à Mobile Services]

Ce didacticiel requiert les éléments suivants :

+ [Kit de développement logiciel (SDK) Mobile Services iOS] et [XCode 4.5][Installer Xcode] et iOS 5.0 ou une version ultérieure.
+ Un compte Microsoft Azure Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Version d'évaluation gratuite Azure</a>.</p></div>

##<a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application GetStartedWithData][GitHub], qui est une application iOS. L'interface utilisateur de cette application est identique à l'application générée par le démarrage IOS de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1. Téléchargez l'exemple d'application [GetStartedWithData][GitHub].

2. Dans Xcode, ouvrez le projet téléchargé et étudiez le fichier TodoService.m.

   	Huit commentaires **// TODO** indiquent les étapes à suivre pour que cette application fonctionne avec votre service mobile.

3. Appuyez sur le bouton **Exécuter** (ou Cmd+R) pour régénérer le projet et démarrer l'application.

4. Dans l'application, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

   	![][0]  

   	Le texte enregistré est affiché dans la liste ci-dessous.

##<a name="create-service"></a>Création d'un service mobile dans le portail de gestion

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Ajout d'une nouvelle table au service mobile

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Test de l'application sur la base de votre nouveau service mobile

1. Dans Xcode, sélectionnez un émulateur vers lequel effectuer le déploiement (via iPhone ou iPad), appuyez sur le bouton **Exécuter** (ou Cmd+R) pour régénérer le projet et démarrer l'application.

   	Cela permet d'exécuter votre client Azure Mobile Services, généré à partir du Kit de développement logiciel (SDK) pour iOS, qui effectue des requêtes sur les éléments auprès de votre service mobile.

2. Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3. Dans le [portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4. Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

   	![][9]

   	Notez que la table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et que des colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour iOS.

##<a name="next-steps"></a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application iOS pour utiliser les données dans Mobile Services.

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

* [Validation et modification de données à l'aide de scripts]
  <br/>Découvrez comment utiliser des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes au moyen de la pagination]
  <br/>Découvrez comment utiliser la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels iOS :

* [Prise en main de l'authentification]
	<br/>Découvrez comment authentifier les utilisateurs de votre application.

* [Prise en main des notifications Push]
  <br/>Apprenez à envoyer une notification Push très basique à votre application avec Mobile Services.

<!-- Anchors. -->
[Téléchargement du projet d'application iOS]: #download-app
[Création du service mobile]: #create-service
[Ajout d'une table de données pour le stockage]: #add-table
[Mise à jour de l'application pour utiliser Mobile Services]: #update-app
[Test de l'application par rapport à Mobile Services]: #test-app
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Validation et modification de données à l'aide de scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-ios
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-ios
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-ios
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-ios

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Installation de Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Kit de développement logiciel (SDK) Mobile Services pour iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Référentiel GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784


<!--HONumber=42-->
