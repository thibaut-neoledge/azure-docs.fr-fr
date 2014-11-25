<properties linkid="mobile-services-html-call-custom-api" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from an HTML client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Appel d'une API personnalisée à partir d'une application HTML

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" >iOS</a><a href="/fr-fr/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a><a href="/fr-fr/documentation/articles/mobile-services-html-call-custom-api" title="HTML" class="current">HTML</a></div>

Cette rubrique explique comment appeler une API personnalisée à partir d'une application HTML. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON.

L'API personnalisée créée dans cette rubrique permet d'envoyer une seule requête POST qui définit l'indicateur complété sur `true` pour toutes les tâches (éléments todo) de la table. En l'absence de cette API personnalisée, le client doit envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données]. À cet effet, vous allez exécuter la procédure suivante :

1.  [Définition de l'API personnalisée][Définition de l'API personnalisée]
2.  [Mise à jour de l'application pour appeler l'API personnalisée][Mise à jour de l'application pour appeler l'API personnalisée]
3.  [Test de l'application][Test de l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez avoir terminé le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données].

## <a name="define-custom-api"></a>Définition de l'API personnalisée

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

## <a name="update-app"></a><span class="short-header">Mise à jour de l'application </span>Mise à jour de l'application pour appeler l'API personnalisée

1.  À l'aide de votre éditeur de texte, ouvrez le fichier index.html, localisez l'élément **button** intitulé `buttonRefresh`, et ajoutez le nouvel élément suivant juste après :

        <button id="buttonCompleteAll">Complete All</button> 

    Le nouveau bouton est ajouté à la page.

2.  Dans page.js, après la fonction **refreshTodoItems**, ajoutez le code suivant :

        var completeAllTodoItems = function () {
            // Asynchronously call the custom API using the POST method.
            client.invokeApi("completeall", {
                body: null,
                method: "post"
            }).done(function (results) {
                var message = results.result.count + " item(s) marked as complete.";
                alert(message);
                refreshTodoItems();
            }, function(error) {
                alert(error.message);
            });
        };

        $('#buttonCompleteAll').click(function () {
            completeAllTodoItems();
        });

    Cette méthode gère l'événement **Click** pour le nouveau bouton. La méthode **invokeApi** est appelée sur le client pour envoyer une requête POST à la nouvelle API personnalisée. Le résultat renvoyé par l'API personnalisée apparaît dans la boîte de message, avec les erreurs éventuelles.

## <a name="test-app"></a>Test de l'application

1.  Actualisez votre navigateur.

2.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

3.  Répétez l'étape précédente jusqu'à ce que vous ayez ajouté plusieurs éléments todo dans la liste.

4.  Cliquez sur le bouton **Complete All**. Un message s'affiche pour indiquer le nombre d'éléments marqués comme terminés, puis la requête filtrée est de nouveau exécutée pour supprimer tous les éléments de la liste.

## Étapes suivantes

Maintenant que vous avez créé une API personnalisée et que vous l'avez appelée à partir de votre application HTML, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

-   [Référence de script serveur Mobile Services][Référence de script serveur Mobile Services]
    Familiarisez-vous avec la création des API personnalisées.




  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-html-get-started
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-html-get-started-data
  [Définition de l'API personnalisée]: #define-custom-api
  [Mise à jour de l'application pour appeler l'API personnalisée]: #update-app
  [Test de l'application]: #test-app
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
