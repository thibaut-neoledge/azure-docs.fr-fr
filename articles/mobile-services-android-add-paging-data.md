<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

Affinage des requêtes Mobile Services au moyen de la pagination
===============================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée vers votre application Android à partir d'Azure Mobile Services. Dans le cadre de ce didacticiel, vous allez utiliser les méthodes de requête **top** et **skip** sur le client pour demander des « pages » spécifiques de données.

**Remarque**

Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-android). Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-android).

1.  Dans Eclipse, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-android).

2.  Dans le menu **Run**, cliquez sur **Run** pour lancer l'application, puis saisissez du texte dans la zone de texte et cliquez sur le bouton **Add**.

3.  Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

4.  Dans le fichier ToDoActivity.java, remplacez la méthode **RefreshTodoItems** par le code suivant :

         private void refreshItemsFromTable() {
             // Définit une requête filtrée qui renvoie les 3 premiers éléments.
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

1.  Régénérez et démarrez l'application.

    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

2.  (Facultatif) Affichez l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou [Fiddler].

     La méthode `top(3)` a été convertie en option de requête `$top=3` dans l'URI de requête.

3.  Mettez à jour la méthode **refreshTodoItems** avec le code suivant :

         private void refreshItemsFromTable() {
             // Définit une requête filtrée qui renvoie les 3 premiers éléments.
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

      Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    **Remarque**

    Il s'agit d'un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes **top** et **skip**. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. Vous pouvez également appeler la méthode **includeInlineCount** pour obtenir le nombre total d'éléments disponibles sur le serveur, avec les données paginées.

4.  (Facultatif) Là aussi, affichez l'URI de la requête envoyée au service mobile.

     La méthode `skip(3)` a été convertie en option de requête `$skip=3` dans l'URI de requête.

Étapes suivantes
----------------

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-android)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-android)
    <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.


