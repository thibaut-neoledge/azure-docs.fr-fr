<properties 
	pageTitle="Appel d'une API personnalisée à partir d'un client HTML - Mobile Services" 
	description="Découvrez comment définir une API personnalisée et l'appeler depuis une application HTML qui utilise Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="" 
	authors="bureado"  
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Appel d'une API personnalisée à partir d'une application HTML

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Cette rubrique explique comment appeler une API personnalisée à partir d'une application HTML. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON.

L'API personnalisée créée dans cette rubrique permet d'envoyer une seule requête POST qui définit l'indicateur complété sur `true` pour toutes les tâches (éléments todo) de la table. En l'absence de cette API personnalisée, le client devrait envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez suivre [Prise en main de Mobile Services] ou [Ajout de Mobile Services à une application existante].

## <a name="define-custom-api"></a>Définir l'API personnalisée

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

##<a name="update-app"></a>Mettre à jour l'application pour appeler l'API personnalisée

1. À l'aide de votre éditeur de texte, ouvrez le fichier index.html, localisez l'élément **button** intitulé `buttonRefresh`, et ajoutez le nouvel élément suivant juste après : 

		<button id="buttonCompleteAll">Complete All</button> 

	Le nouveau bouton est ajouté à la page.

2. Dans page.js, après la fonction **refreshTodoItems**, ajoutez le code suivant :

		var completeAllTodoItems = function () {
			// Asynchronously call the custom API using the POST method.
			client.invokeApi("completeall", {
				body: null,
				method: "post"
			}).done(function (results) {
				var message = results.result.count + " item(s) marked as complete.";
				alert(message);
				refreshTodoItems();
			}, function(error) {
				alert(error.message);
			});
		};

		$('#buttonCompleteAll').click(function () {
			completeAllTodoItems();
		});

	Cette méthode gère l'événement **Click** pour le nouveau bouton. La méthode **invokeApi** est appelée sur le client pour envoyer une requête POST à la nouvelle API personnalisée. Le résultat renvoyé par l'API personnalisée apparaît dans la boîte de message, avec les erreurs éventuelles.

## <a name="test-app"></a>Test de l'application

1. Actualisez votre navigateur.

2. Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

3. Répétez l'étape précédente jusqu'à ce que vous ayez ajouté plusieurs éléments todo dans la liste.

4. Cliquez sur le bouton **Complete All**. Un message s'affiche pour indiquer le nombre d'éléments marqués comme terminés, puis la requête filtrée est de nouveau exécutée pour supprimer tous les éléments de la liste.

## Étapes suivantes

Cette rubrique vous a montré comment utiliser la fonction **invokeApi** pour appeler une API personnalisée relativement simple à partir de votre application HTML/JavaScript. Pour en savoir plus sur l’utilisation de la fonction **invokeApi**, consultez le billet [API personnalisée dans Azure Mobile Services](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Pour plus d'informations sur les rubriques Mobile Services suivantes, consultez :

* [Référence de script serveur Mobile Services] <br/>Familiarisez-vous avec la création des API personnalisées.

* [Stockage des scripts serveur dans le contrôle du code source ] <br/> Apprenez à utiliser la fonctionnalité de contrôle du code source pour développer et publier plus facilement et de manière plus sécurisée le code de script de l'API personnalisée.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Prise en main de Mobile Services]: mobile-services-html-get-started.md
[Ajout de Mobile Services à une application existante]: mobile-services-html-get-started-data.md
[Stockage des scripts serveur dans le contrôle du code source ]: mobile-services-store-scripts-source-control.md

<!---HONumber=July15_HO1-->