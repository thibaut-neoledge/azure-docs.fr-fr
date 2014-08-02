<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

Affinage des requêtes Mobile Services au moyen de la pagination
===============================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée à votre application HTML par Azure Mobile Services. Dans le cadre de ce didacticiel, vous allez utiliser les méthodes de requête **Take** et **Skip** sur le client pour demander des « pages » spécifiques de données.

**Remarque**

Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-html). Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-html).

1.  Exécutez l'un des fichiers de commandes suivants à partir du sous-dossier **server** du projet que vous avez modifié dans le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-html).

    -   **launch-windows** (pour les ordinateurs Windows)
    -   **launch-mac.command** (pour les ordinateurs Mac OS X)
    -   **launch-linux.sh** (pour les ordinateurs Linux)

    **Remarque**

    Sur un ordinateur Windows, appuyez sur la touche « R » lorsque PowerShell vous demande de confirmer l'exécution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas exécuter le script, car il a été téléchargé depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer à charger le script.

    Un serveur Web démarre sur votre ordinateur local pour héberger l'application.

2.  Dans un navigateur Web, accédez à <http://localhost:8000/>, puis tapez du texte dans **Ajouter une nouvelle tâche** et cliquez sur **Ajouter**.

3.  Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

4.  Dans le fichier app.js, remplacez la définition de la variable `query` dans la méthode **refreshTodoItems** par la ligne de code suivante :

         var query = todoItemTable.where({ complete: false }).take(3);

  Cette requête, lorsqu'elle est exécutée, renvoie les trois premiers éléments marqués comme terminés.

1.  Revenez au navigateur Web et rechargez la page.

  Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

1.  (Facultatif) Affichez l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou [Fiddler].

     Notez que la méthode **take(3)** a été convertie en option de requête **$top=3** dans l'URI de requête.

2.  Mettez de nouveau à jour la requête avec le code suivant :

         var query = todoItemTable.where({ complete: false }).skip(3).take(3);

3.  Revenez au navigateur Web et rechargez la page.

      Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    **Remarque**

    Ce didacticiel utilise un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes **Take** et **Skip**. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. Vous pouvez également appeler la méthode **includeTotalCount** pour obtenir le nombre total d'éléments disponibles sur le serveur, avec les données paginées.

4.  (Facultatif) Là aussi, affichez l'URI de la requête envoyée au service mobile.

     Notez que la méthode **skip(3)** a été convertie en option de requête **$skip=3** dans l'URI de requête.

Étapes suivantes
----------------

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. À présent, apprenez à authentifier les utilisateurs de votre application en consultant le didacticiel [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-html). Obtenez plus d'informations sur l'utilisation de Mobile Services avec HTML/JavaScript dans le [Guide de fonctionnement Mobile Services HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)

