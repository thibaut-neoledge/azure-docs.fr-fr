<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Affinage des requêtes Mobile Services au moyen de la pagination

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée à votre application HTML par Azure Mobile Services. Dans ce didacticiel, vous allez utiliser les méthodes de requête **Take** et **Skip** sur le client pour demander des « pages » spécifiques de données.

<div class="dev-callout"><b>Remarque</b>
<p>Pour &eacute;viter tout d&eacute;passement de capacit&eacute; dans les appareils mobiles clients, Mobile Services impl&eacute;mente une limite automatique du nombre de pages, qui autorise par d&eacute;faut un maximum de 50&nbsp;&eacute;l&eacute;ments par r&eacute;ponse. En sp&eacute;cifiant la taille de page, vous pouvez demander explicitement jusqu'&agrave; 1&nbsp;000&nbsp;&eacute;l&eacute;ments dans la r&eacute;ponse.</p>
</div>

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données][Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données][Prise en main des données].

1.  Exécutez l'un des fichiers de commandes suivants à partir du sous-dossier **server** du projet que vous avez modifié dans le didacticiel [Prise en main des données][Prise en main des données].

    -   **launch-windows** (pour les ordinateurs Windows)
    -   **launch-mac.command** (pour les ordinateurs Mac OS X)
    -   **launch-linux.sh** (pour les ordinateurs Linux)

    <div class="dev-callout"><b>Remarque</b>
    <p>Sur un ordinateur Windows, appuyez sur la touche &laquo;&nbsp;R&nbsp;&raquo; lorsque PowerShell vous demande de confirmer l'ex&eacute;cution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas ex&eacute;cuter le script, car il a &eacute;t&eacute; t&eacute;l&eacute;charg&eacute; depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer &agrave; charger le script.</p>
</div>

    Un serveur Web démarre sur votre ordinateur local pour héberger l'application.

2.  Dans un navigateur Web, accédez à <http://localhost:8000/>, puis tapez du texte dans **Ajouter une nouvelle tâche** et cliquez sur **Ajouter**.

3.  Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

4.  Dans le fichier app.js, remplacez la définition de la variable `query` dans la méthode **refreshTodoItems** par la ligne de code suivante :

        var query = todoItemTable.where({ complete: false }).take(3);

    Cette requête, lorsqu'elle est exécutée, renvoie les trois premiers éléments marqués comme terminés.

5.  Revenez au navigateur Web et rechargez la page.

    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

6.  (Facultatif) Affichez l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou [Fiddler].

    La méthode **take(3)** a été convertie en option de requête **$top=3** dans l'URI de requête.

7.  Mettez de nouveau à jour la requête avec le code suivant :

        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

8.  Revenez au navigateur Web et rechargez la page.

    Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    <div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel s'appuie sur un sc&eacute;nario simplifi&eacute; dans lequel les valeurs de pagination cod&eacute;es en dur sont transmises aux m&eacute;thodes <strong>Take</strong> et <strong>Skip</strong>. Dans une application r&eacute;elle, vous pouvez utiliser des requ&ecirc;tes semblables &agrave; celles indiqu&eacute;es plus haut avec un contr&ocirc;le pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'acc&eacute;der aux pages pr&eacute;c&eacute;dentes et suivantes.  Vous pouvez &eacute;galement appeler la m&eacute;thode <strong>includeTotalCount</strong> pour obtenir le nombre total d'&eacute;l&eacute;ments disponibles sur le serveur, avec les donn&eacute;es pagin&eacute;es.</p>
</div>

9.  (Facultatif) Là aussi, affichez l'URI de la requête envoyée au service mobile.

    La méthode **skip(3)** a été convertie en option de requête **$skip=3** dans l'URI de requête.

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. À présent, apprenez à authentifier les utilisateurs de votre application en consultant le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification]. Obtenez plus d'informations sur l'utilisation de Mobile Services avec HTML/JavaScript dans le [Guide de fonctionnement Mobile Services HTML/JavaScript][Guide de fonctionnement Mobile Services HTML/JavaScript]

  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-html
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-html
  [Guide de fonctionnement Mobile Services HTML/JavaScript]: /fr-fr/develop/mobile/how-to-guides/work-with-html-js-client
