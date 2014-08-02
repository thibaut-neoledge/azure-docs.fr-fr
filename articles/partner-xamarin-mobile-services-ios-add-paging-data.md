<properties linkid="" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="" solutions="" manager="" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

Affinage des requêtes Mobile Services au moyen de la pagination
===============================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[iOS C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Android C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée à votre application iOS par Azure Mobile Services. Dans le cadre de ce didacticiel, vous allez utiliser les propriétés de requête **Skip** et **Take** sur le client pour demander des « pages » spécifiques de données.

**Remarque**

Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios). Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios).

1.  Dans Xamarin Studio, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios).

2.  Cliquez sur **Exécuter** pour générer le projet et démarrer l'application, puis entrez du texte dans la zone de texte et cliquez sur l'icône plus (**+**).

3.  Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

4.  Ouvrez le fichier **TodoService.cs**, recherchez la méthode **RefreshDataAsync** et remplacez la requête LINQ dans la table `todoTable` par la requête suivante :

             Items = await todoTable
                             .Where(todoItem => todoItem.Complete == false)
                            .Take(3)
                            .ToListAsync();

    Cette requête renvoie les trois premiers éléments qui ne sont pas marqués comme terminés. 

5.  Régénérez et démarrez l'application.

    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

6.  Mettez à jour une nouvelle fois la requête LINQ dans la méthode **RefreshDataAsync** avec les éléments suivants :

             Items = await todoTable
                             .Where(todoItem => todoItem.Complete == false)
                             .Skip(3)
                             .Take(3)
                             .ToListAsync();

    Cette fois, attribuez la valeur 3 à **Skip**. 

    Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    **Remarque**

    Ce didacticiel utilise un scénario simplifié dans lequel des valeurs de pagination codées en dur sont définies pour les propriétés **Skip** et **Take**. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. Vous pouvez également appeler la méthode **IncludeTotalCount** pour obtenir le nombre total de tous les éléments disponibles sur le serveur, ainsi que les données paginées.

Étapes suivantes
----------------

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations, consultez la rubrique Mobile Services suivante :

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

<!-- Anchors. -->

[Next Steps]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Get started with Mobile Services]: ./en-us/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Management Portal]: https://manage.windowsazure.com/
