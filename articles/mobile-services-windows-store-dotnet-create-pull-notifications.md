<properties urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Définition d'une API personnalisée qui prend en charge les notifications Pull - Azure Mobile Services" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# Définition d'une API personnalisée qui prend en charge les notifications périodiques

<div class="dev-center-tutorial-selector"> 
	<a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications/" title="Windows Store JavaScript">Windows Store JavaScript</a>
</div>

Cette rubrique explique comment appeler une API personnalisée afin de prendre en charge des notifications périodiques à partir d'une application Windows Store. Si les notifications périodiques sont activées, Windows accédera régulièrement au point de terminaison de votre API personnalisée et utilisera le XML renvoyé, dans un format mosaïque, pour mettre à jour la vignette de l'application dans le menu Démarrer. Pour plus d'informations, consultez la page [Notifications périodiques]. 

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Prise en main de Mobile Services] ou [Ajout de Mobile Services à une application existante]. À cet effet, vous allez exécuter la procédure suivante :

1. [Définition de l'API personnalisée]
2. [Mise à jour de l'application pour activer les notifications périodiques]
3. [Test de l'application] 

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez suivre [Prise en main de Mobile Services] ou [Ajout de Mobile Services à une application existante].  

## <a name="define-custom-api"></a>Définition de l'API personnalisée

1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

   	![][0]

2. Cliquez sur l'onglet **API**, puis sur **Créer une API personnalisée**.

   	![][1]

	La boîte de dialogue **Créer un nouvel API personnalisé** s'affiche.

3. Définissez **Autorisation Get** sur **Tout le monde**, tapez _tiles_ dans **Nom API**, puis cliquez sur le bouton de vérification.

   	![][2]

	La nouvelle API est créée avec un accès en lecture (GET) public.

4. Cliquez sur la nouvelle entrée tiles dans la table API.

	![][3]

5. Cliquez sur l'onglet **Script** et remplacez le code existant par les lignes suivantes :

		exports.get = function(request, response) {
		    var wns = require('wns');
		    var todoItems = request.service.tables.getTable('TodoItem');
		    todoItems.where({
		        complete: false
		    }).read({
		        success: sendResponse
		    });
		
		    function sendResponse(results) {
		        var tileText = {
		            text1: "My todo list"
		        };
		        var i = 0;
		        console.log(results)
		        results.forEach(function(item) {
		            tileText["text" + (i + 2)] = item.text;
		            i++;
		        });
		        var xml = wns.createTileSquareText01(tileText);
		        response.set('content-type', 'application/xml');
		        response.send(200, xml);
		    }
		};

	Ce code renvoie les trois premiers éléments non terminés de la table TodoItem, puis les charge dans un objet JSON transmis à la fonction **wns**.**createTileSquareText01**. Cette fonction renvoie le code XML de modèle de vignette suivant :

		<tile>
			<visual>
				<binding template="TileSquareText01">




				</binding>
			</visual>
		</tile>

	La fonction **exports.get** est utilisée parce que le client enverra une requête GET pour accéder au modèle de vignette.

   	<div class="dev-callout"><b>Remarque</b>
   		<p>Ce script d'API personnalisée utilise le <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">module wns</a>Node.js, qui est référencé à l'aide de la fonction <strong>require</strong> . Ce module est différent de l' <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">objet wns</a> renvoyé par l' <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554217.aspx">objet push</a>, qui permet d'envoyer des notifications Push à partir de scripts serveur.</p>
   	</div>

Vous allez ensuite modifier l'application de démarrage rapide pour démarrer des notifications périodiques qui mettent à jour la vignette dynamique en demandant la nouvelle API personnalisée.

<h2><a name="update-app"></a>Mise à jour de l'application pour activer les notifications périodiques</h2>

1. Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application de démarrage rapide du précédent didacticiel.

2. Assurez-vous qu'un élément au moins est affiché. Si aucun élément n'est présent, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

3. Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l'instruction using suivante :

		using Windows.UI.Notifications;

4. Ajoutez le code suivant dans le gestionnaire d'événements **OnLaunched** :

        TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
            new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
            PeriodicUpdateRecurrence.Hour
        );

	Ce code active les notifications périodiques pour demander les données du modèle de vignette à la nouvelle API personnalisée **tiles**. Sélectionnez la valeur [PeriodicUpdateRecurrance] qui correspond le mieux à la fréquence de mise à jour de vos données.

## <a name="test-app"></a>Test de l'application

1. Dans Visual Studio, appuyez sur la touche F5 pour réexécuter l'application.

	Les notifications périodiques sont alors activées.

2. Accédez à l'écran d'accueil et recherchez la vignette dynamique correspondant à l'application. Comme vous pouvez le constater, les données de l'élément sont maintenant affichées dans la vignette.

 	![][4]

## Étapes suivantes

Maintenant que vous avez créé une notification périodique, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

* [Prise en main des notifications Push]
	<br/>Les notifications périodiques sont gérées par Windows et obéissent à une planification prédéfinie. Les notifications Push peuvent être envoyées par le service mobile à la demande. Il peut s'agir de notifications toast, par vignette et brutes.

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur la création d'API personnalisées.

* [Guide de fonctionnement Mobile Services .NET]
  <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET

<!-- Anchors. -->
[Définition de l'API personnalisée]: #define-custom-api
[Mise à jour de l'application pour activer les notifications périodiques]: #update-app
[Test de l'application]: #test-app
[Étapes suivantes]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Notifications Push Windows et Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Tableau de bord Mes applications]: http://go.microsoft.com/fwlink/?LinkId=262039
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started
[Ajout de Mobile Services à une application existante]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Notifications périodiques]: http://msdn.microsoft.com/fr-fr/library/windows/apps/jj150587.aspx

[Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


<!--HONumber=35_1-->
