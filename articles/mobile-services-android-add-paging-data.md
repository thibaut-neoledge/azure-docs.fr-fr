<properties pageTitle="Ajout d'une pagination aux données (Android) | Centre de développement mobile" description="Découvrez comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application Android à partir de Mobile Services." services="mobile-services" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="ricksal"/>

# Affinage des requêtes Mobile Services au moyen de la pagination

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée vers votre application Android à partir d'Azure Mobile Services. Dans ce didacticiel, vous allez utiliser les méthodes de requête **top** et **skip** sur le client pour demander des " pages " de données spécifiques.

> [AZURE.NOTE] Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel se fonde sur les étapes et l'exemple d'application du didacticiel précédent, [Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé, [Prise en main des données]. 

1. Dans Eclipse, ouvrez le projet que vous avez créé lorsque vous avez suivi le didacticiel [Prise en main des données].

2. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application, puis entrez un texte dans la zone de texte et cliquez sur le bouton **Ajouter**.

3. Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem. 

4. Dans le fichier java ToDoActivity.java, remplacez la méthode **RefreshTodoItems** par le code suivant :

		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).top(3)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

  	Cette requête renvoie les trois premiers éléments qui ne sont pas marqués comme terminés.

5. Régénérez et démarrez l'application. 
   Notez que seuls les trois premiers résultats de la table TodoItem sont affichés. 

6. (Facultatif) Affichez l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou [Fiddler]. 

   	Notez que la méthode `top(3)` a été convertie en option de requête `$top=3` dans l'URI de requête.

7. Mettez à jour la méthode **RefreshTodoItems** avec le code suivant :
        
		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).skip(3).top(3)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

   	Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième " page " de données, dont la taille est de trois éléments.
> [AZURE.NOTE] Ce didacticiel utilise un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes **top** et **skip**. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. Vous pouvez également appeler la méthode **includeInlineCount** pour obtenir le nombre total d'éléments disponibles sur le serveur, avec les données paginées.
8. (Facultatif) Là aussi, affichez l'URI de la requête envoyée au service mobile. 

   	La méthode  `skip(3)` a été convertie en option de requête `$skip=3` dans l'URI de requête.

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations sur les rubriques Mobile Services suivantes :

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.
 
* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

<!-- Anchors. -->

[Étapes suivantes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-android
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-android
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android

[Portail de gestion]: https://manage.windowsazure.com/



<!--HONumber=42-->
