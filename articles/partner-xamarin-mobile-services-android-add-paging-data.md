<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin Android app from Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" />

Affinage des requêtes Mobile Services au moyen de la pagination
===============================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[iOS C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Android C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application Xamarin.Android à partir d'Azure Mobile Services. Dans ce didacticiel, vous allez utiliser les méthodes de requête **Take** et **Skip** sur le client pour demander des « pages » spécifiques de données.

**Remarque**

Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android). Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android).

1.  Dans Xamarin Studio, ouvrez le projet que vous avez créé en suivant le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android).

2.  Cliquez sur **Run** pour démarrer l'application, puis entrez le texte dans la zone de texte et cliquez sur le bouton **Add**.

3.  Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

4.  Dans le fichier **TodoActivity.cs**, remplacez la requête LINQ dans la méthode **RefreshItemsFromTableAsync** par la requête suivante :

		  var list = await todoTable.Where(item => item.Complete == false)
                                   .Take(3)
                                   .ToListAsync();

	Cette requête renvoie les trois premiers éléments qui ne sont pas marqués comme terminés.

5.  Régénérez et démarrez l'application.

    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

6.  (Facultatif) Affichez l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou [Fiddler].

    La méthode « Take(3) » a été convertie en option de requête « $top=3 » dans l'URI de requête.

7.  Mettez à jour la requête LINQ dans la méthode **RefreshItemsFromTableAsync** une nouvelle fois avec la requête suivante :

         var list = await todoTable.Where(item => item.Complete == false)
                                       .Skip(3)
                                       .Take(3)
                                      .ToListAsync();

    Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    **Remarque**

    Ce didacticiel s'appuie sur un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes **Take** et **Skip**. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. Vous pouvez également appeler la méthode **IncludeTotalCount** pour obtenir le nombre total d'éléments disponibles sur le serveur, avec les données paginées.

8.  (Facultatif) Là aussi, affichez l'URI de la requête envoyée au service mobile.

    La méthode « Skip(3) » a été convertie en option de requête « $skip=3 » dans l'URI de requête.

Étapes suivantes
----------------

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android)
    <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.


<!-- Anchors. -->

[Next Steps]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-xamarin-android
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Management Portal]: https://manage.windowsazure.com/

