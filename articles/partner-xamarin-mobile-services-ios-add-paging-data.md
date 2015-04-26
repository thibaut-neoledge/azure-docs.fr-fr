<properties 
	pageTitle="Ajout de la pagination aux données (Xamarin.iOS) | Centre de développement mobile" 
	description="Découvrez comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application Xamarin iOS à partir de Mobile Services." 
	services="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>


# Affinage des requêtes Mobile Services au moyen de la pagination

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Cette rubrique vous présente l'utilisation de la pagination pour gérer la quantité de données renvoyée à votre application iOS par Azure Mobile Services. Dans le cadre de ce didacticiel, vous allez utiliser les propriétés de requête **Skip** et **Take** sur le client pour demander des " pages " spécifiques de données.

> [AZURE.NOTE] Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données]. 

1. Dans Xamarin Studio, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données].

2. Cliquez sur **Exécuter** pour générer le projet et démarrer l'application, puis entrez du texte dans la zone de texte et cliquez sur l'icône plus (**+**).

3. Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem. 

4. Ouvrez le fichier **TodoService.cs**, recherchez la méthode **RefreshDataAsync**, et remplacez la requête LINQ dans la table <code>todoTable</code> par la requête suivante : 

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
        		            .Take(3)
                		    .ToListAsync();

   	Cette requête renvoie les trois premiers éléments qui ne sont pas marqués comme terminés. 

5. Régénérez et démarrez l'application. 
   
    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés. 

7. Mettez à jour une nouvelle fois la requête LINQ dans la méthode **RefreshDataAsync** avec les éléments suivants :

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
		                    .Skip(3)
		                    .Take(3)
		                    .ToListAsync();

   	Cette fois, définissez la valeur **Skip** sur 3. 

   	Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième " page " de données, dont la taille est de trois éléments.

    > [AZURE.NOTE] Ce didacticiel utilise un scénario simplifié dans lequel des valeurs de pagination codées en dur sont définies pour les propriétés **Skip** et **Take**. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. Vous pouvez également appeler la méthode **IncludeTotalCount** pour obtenir le nombre total de tous les éléments disponibles sur le serveur, ainsi que les données paginées.

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations, consultez la rubrique Mobile Services suivante :

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.
 
<!--
* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.
-->

<!-- Anchors. -->

[Étapes suivantes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Prise en main de Mobile Services]: ./fr-fr/develop/mobile/tutorials/get-started-xamarin-ios
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portail de gestion]: https://manage.windowsazure.com/



<!--HONumber=42-->
