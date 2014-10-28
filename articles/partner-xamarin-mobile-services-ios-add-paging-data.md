<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-ios" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="donnam" solutions="" manager="dwrede" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Affinage des requêtes Mobile Services au moyen de la pagination

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Android C#</a>
</div>

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée à votre application iOS par Azure Mobile Services. Dans le cadre de ce didacticiel, vous allez utiliser les propriétés de requête **Skip** et **Take** sur le client pour demander des « pages » spécifiques de données.

<div class="dev-callout"><b>Remarque</b>
<p>Pour &eacute;viter tout d&eacute;passement de capacit&eacute; dans les appareils mobiles clients, Mobile Services impl&eacute;mente une limite automatique du nombre de pages, qui autorise par d&eacute;faut un maximum de 50&nbsp;&eacute;l&eacute;ments par r&eacute;ponse. En sp&eacute;cifiant la taille de page, vous pouvez demander explicitement jusqu'&agrave; 1&nbsp;000&nbsp;&eacute;l&eacute;ments dans la r&eacute;ponse.</p>
</div>

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données][]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données][].

1.  Dans Xamarin Studio, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données][].

2.  Cliquez sur **Exécuter** pour générer le projet et démarrer l'application, puis entrez du texte dans la zone de texte et cliquez sur l'icône plus (**+**).

3.  Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

4.  Ouvrez le fichier **TodoService.cs**, recherchez la méthode **RefreshDataAsync** et remplacez la requête LINQ dans la table `todoTable` par la requête suivante :

            Items = await todoTable
                            .Where (todoItem => todoItem.Complete == false)
                            .Take(3)
                            .ToListAsync();

    Cette requête renvoie les trois premiers éléments qui ne sont pas marqués comme terminés.

5.  Régénérez et démarrez l'application.

    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

6.  Mettez à jour une nouvelle fois la requête LINQ dans la méthode **RefreshDataAsync** avec les éléments suivants :

            Items = await todoTable
                            .Where (todoItem => todoItem.Complete == false)
                            .Skip(3)
                            .Take(3)
                            .ToListAsync();

    Cette fois, définissez la valeur **Skip** sur 3.

    Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    <div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel utilise un sc&eacute;nario simplifi&eacute; dans lequel des valeurs de pagination cod&eacute;es en dur sont d&eacute;finies pour les propri&eacute;t&eacute;s <strong>Skip</strong> et <strong>Take</strong>. Dans une application r&eacute;elle, vous pouvez utiliser des requ&ecirc;tes semblables &agrave; celles indiqu&eacute;es plus haut avec un contr&ocirc;le pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'acc&eacute;der aux pages pr&eacute;c&eacute;dentes et suivantes. Vous pouvez &eacute;galement appeler la m&eacute;thode <strong>IncludeTotalCount</strong> pour obtenir le nombre total de tous les &eacute;l&eacute;ments disponibles sur le serveur, ainsi que les donn&eacute;es pagin&eacute;es.</p>
</div>

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations, consultez la rubrique Mobile Services suivante :

-   [Prise en main de l'authentification][]
    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

  [Windows Store C#]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [iOS C#]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [Android C#]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
