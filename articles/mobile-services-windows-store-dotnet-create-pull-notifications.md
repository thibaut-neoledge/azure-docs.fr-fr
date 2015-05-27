<properties 
	pageTitle="Définition d'une API personnalisée qui prend en charge les notifications Pull - Azure Mobile Services" 
	description="Découvrez comment définir une API personnalisée qui prend en charge les notifications périodiques dans les applications Windows Store utilisant Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="glenga"/>

# Définition d'une API personnalisée qui prend en charge les notifications périodiques

<div class="dev-center-tutorial-selector"> 
	<a href="/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications/" title="Windows Store JavaScript">Windows Store JavaScript</a>
</div>

Cette rubrique explique comment appeler une API personnalisée afin de prendre en charge des notifications périodiques à partir d'une application Windows Store. Si les notifications périodiques sont activées, Windows accédera régulièrement au point de terminaison de votre API personnalisée et utilisera le XML renvoyé, dans un format mosaïque, pour mettre à jour la vignette de l'application dans le menu Démarrer. Pour plus d'informations, consultez la page [Notifications périodiques].

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec l'un des deux didacticiels [Prise en main de Mobile Services] ou [Ajout de Mobile Services à une application existante]. À cet effet, vous allez exécuter la procédure suivante :

1. [Définir l'API personnalisée]
2. [Mettre à jour l'application pour activer les notifications périodiques]
3. [Tester l'application] 

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez suivre l'un des deux didacticiels [Prise en main de Mobile Services] ou [Ajout de Mobile Services à une application existante].

## <a name="define-custom-api"></a>Définir l'API personnalisée

1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l’application.

   	![][0]

2. Cliquez sur l’onglet **API**, puis sur **Create a custom API**.

   	![][1]

	La boîte de dialogue **Create a new custom API** s’affiche.

3. Définissez **Autorisation GET** sur **Tout le monde**, tapez _tiles_ dans **Nom API**, puis cliquez sur le bouton de vérification.

   	![][2]

	La nouvelle API est créée avec un accès en lecture (GET) public.

4. Cliquez sur la nouvelle entrée tiles dans la table API.

	![][3]

5. Cliquez sur l'onglet **Script** et remplacez le code existant par les lignes suivantes :

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

	Ce code renvoie les 3 premiers éléments non terminés de la table TodoItem, puis les charge dans un objet JSON transmis à la fonction **wns**.**createTileSquareText01**. Cette fonction renvoie le code XML de modèle de vignette suivant :

		<tile>
			<visual>
				<binding template="TileSquareText01">
					<text id="1">My todo list</text>
					<text id="2">Task 1</text>
					<text id="3">Task 2</text>
					<text id="4">Task 3</text>
				</binding>
			</visual>
		</tile>

	La fonction **exports.get** est utilisée parce que le client enverra une requête GET pour accéder au modèle de vignette.

   	> [AZURE.NOTE]Ce script d'API personnalisé utilise le [module wns](http://go.microsoft.com/fwlink/p/?LinkId=306750) Node.js, qui est référencé en utilisant la fonction **require**. Ce module est différent de l'[objet wns](http://go.microsoft.com/fwlink/p/?LinkId=260591) renvoyé par l'[objet push](http://msdn.microsoft.com/library/windowsazure/jj554217.aspx), lequel est utilisé pour envoyer des notifications push à partir de scripts serveur.

Vous allez ensuite modifier l'application de démarrage rapide pour démarrer des notifications périodiques qui mettent à jour la vignette dynamique en demandant la nouvelle API personnalisée.

##<a name="update-app"></a>Mettre à jour l'application pour activer les notifications périodiques

1. Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application de démarrage rapide du précédent didacticiel.

2. Assurez-vous qu'un élément au moins est affiché. Si aucun élément n'est présent, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

3. Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l'instruction using suivante :

		using Windows.UI.Notifications;

4. Ajoutez le code suivant dans le gestionnaire d'événements **OnLaunched** :

        TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
            new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
            PeriodicUpdateRecurrence.Hour
        );

	Ce code active les notifications périodiques pour demander les données du modèle de vignette à la nouvelle API personnalisée **tiles**. Sélectionnez la valeur [PeriodicUpdateRecurrance] qui correspond le mieux à la fréquence de mise à jour de vos données.

## <a name="test-app"></a>Tester l'application

1. Dans Visual Studio, appuyez sur la touche F5 pour réexécuter l'application.

	Les notifications périodiques sont alors activées.

2. Accédez à l'écran d'accueil et recherchez la vignette dynamique correspondant à l'application. Comme vous pouvez le constater, les données de l'élément sont maintenant affichées dans la vignette.

 	![][4]

## Étapes suivantes

Maintenant que vous avez créé une notification périodique, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

* [Prise en main des notifications Push] <br/>Les notifications périodiques sont gérées par Windows et obéissent à une planification prédéfinie. Les notifications Push peuvent être envoyées par le service mobile à la demande. Il peut s'agir de notifications toast, par vignette et brutes.

* [Référence de script serveur Mobile Services] <br/>Familiarisez-vous avec la création des API personnalisées.

* [Guide de fonctionnement Mobile Services .NET] <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.

<!-- Anchors. -->
[Définir l'API personnalisée]: #define-custom-api
[Mettre à jour l'application pour activer les notifications périodiques]: #update-app
[Tester l'application]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Prise en main de Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Ajout de Mobile Services à une application existante]: mobile-services-windows-store-dotnet-get-started.md
[Prise en main des notifications Push]: mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md

[portail de gestion Azure]: https://manage.windowsazure.com/
[Notifications périodiques]: http://msdn.microsoft.com/library/windows/apps/jj150587.aspx

[Guide de fonctionnement Mobile Services .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md


<!--HONumber=54-->