<properties 
	pageTitle="Ajout de la pagination aux données (Xamarin Android) | Centre de développement mobile" 
	description="Découvrez comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application Xamarin Android à partir de Mobile Services. 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="donnam"/>

# Affinage des requêtes Mobile Services au moyen de la pagination

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]


Cette rubrique vous présente l'utilisation de la pagination pour gérer la quantité de données renvoyées vers votre application Xamarin.Android à partir d'Azure Mobile Services. Dans ce didacticiel, vous allez utiliser les méthodes de requête **Take** et **Skip** sur le client pour demander des " pages " spécifiques de données.

> [AZURE.NOTE] Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données]. 

1. Dans Xamarin Studio, ouvrez le projet que vous avez créé en suivant le didacticiel [Prise en main des données].

2. Cliquez sur **Run** pour démarrer l'application, puis entrez le texte dans la zone de texte et cliquez sur le bouton **Add**.

3. Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem. 

4. Dans le fichier **TodoActivity.cs**, remplacez la requête LINQ dans la méthode **RefreshItemsFromTableAsync** par la requête suivante :

		var list = await todoTable.Where(item => item.Complete == false)
						          .Take(3)
			                      .ToListAsync();

	  Cette requête renvoie les trois premiers éléments qui ne sont pas marqués comme terminés.

5. Régénérez et démarrez l'application. 
   
    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés. 

6. (Facultatif) Affichez l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tels que les outils destinés aux développeurs de navigateurs ou [Fiddler]. 

   	La méthode  `Take(3)` a été convertie en option de requête `$top=3` dans l'URI de la requête.

7. Mettez à jour la requête LINQ dans la méthode **RefreshItemsFromTableAsync** une nouvelle fois avec la requête suivante :
            
			var list = await todoTable.Where(item => item.Complete == false)
							          .Skip(3)
				                      .Take(3)
                 				      .ToListAsync();

   	Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième " page " de données, dont la taille est de trois éléments.

    > [AZURE.NOTE] Ce didacticiel s'appuie sur un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes **Take** et **Skip**. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. Vous pouvez également appeler la méthode **IncludeTotalCount** pour obtenir le nombre total d'éléments disponibles sur le serveur, avec les données paginées.

8. (Facultatif) Là aussi, affichez l'URI de la requête envoyée au service mobile. 

   	Notez que la méthode  `Skip(3)` a été convertie en option de requête `$skip=3` dans l'URI de requête.

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
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-android
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Portail de gestion]: https://manage.windowsazure.com/



<!--HONumber=42-->
