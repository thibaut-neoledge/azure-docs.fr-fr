<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Affinage des requêtes Mobile Services au moyen de la pagination

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée vers votre application Android à partir d'Azure Mobile Services. Dans le cadre de ce didacticiel, vous allez utiliser les méthodes de requête **top** et **skip** sur le client pour demander des « pages » spécifiques de données.

<div class="dev-callout"><b>Remarque</b>
<p>Pour &eacute;viter tout d&eacute;passement de capacit&eacute; dans les appareils mobiles clients, Mobile Services impl&eacute;mente une limite automatique du nombre de pages, qui autorise par d&eacute;faut un maximum de 50&nbsp;&eacute;l&eacute;ments par r&eacute;ponse. En sp&eacute;cifiant la taille de page, vous pouvez demander explicitement jusqu'&agrave; 1&nbsp;000&nbsp;&eacute;l&eacute;ments dans la r&eacute;ponse.</p>
</div>

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données][Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données][Prise en main des données].

1.  Dans Eclipse, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main des données][Prise en main des données].

2.  Dans le menu **Run**, cliquez sur **Run** pour lancer l'application, puis saisissez du texte dans la zone de texte et cliquez sur le bouton **Add**.

3.  Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

4.  Dans le fichier ToDoActivity.java, remplacez la méthode **RefreshTodoItems** par le code suivant :

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

5.  Régénérez et démarrez l'application.

    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

6.  (Facultatif) Affichez l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou [Fiddler].

    Notez que la méthode `top(3)` a été convertie en option de requête `$top=3` dans l'URL de requête.

7.  Mettez à nouveau à jour la méthode **RefreshTodoItems** avec le code suivant :

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

    Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    <div class="dev-callout"><b>Remarque</b>
<p>Il s'agit d'un sc&eacute;nario simplifi&eacute; dans lequel les valeurs de pagination cod&eacute;es en dur sont transmises aux m&eacute;thodes <strong>top</strong> et <strong>skip</strong>. Dans une application r&eacute;elle, vous pouvez utiliser des requ&ecirc;tes semblables &agrave; celles indiqu&eacute;es plus haut avec un contr&ocirc;le pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'acc&eacute;der aux pages pr&eacute;c&eacute;dentes et suivantes. Vous pouvez &eacute;galement appeler la m&eacute;thode <strong>includeInlineCount</strong> pour obtenir le nombre total d'&eacute;l&eacute;ments disponibles sur le serveur, avec les donn&eacute;es pagin&eacute;es.</p>
</div>

8.  (Facultatif) Là aussi, affichez l'URI de la requête envoyée au service mobile.

    Notez que la méthode `skip(3)` a été convertie en option de requête `$skip=3` dans l'URL de requête.

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Prise en main des notifications Push][Prise en main des notifications Push]
  
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.


  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-android
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android
