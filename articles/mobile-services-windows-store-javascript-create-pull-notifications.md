<properties linkid="develop-mobile-tutorials-create-pull-notifications-js" urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Define a custom API that supports pull notifications - Azure Mobile Services" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Définition d'une API personnalisée qui prend en charge les notifications périodiques

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/fr-fr/develop/mobile/tutorials/create-pull-notifications-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/develop/mobile/tutorials/create-pull-notifications-js" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a>
</div>

Cette rubrique explique comment appeler une API personnalisée afin de prendre en charge des notifications périodiques à partir d'une application Windows Store. Si les notifications périodiques sont activées, Windows accédera régulièrement au point de terminaison de votre API personnalisée et utilisera le XML renvoyé, dans un format mosaïque, pour mettre à jour la vignette de l'application dans le menu Démarrer. Pour plus d'informations, consultez la page [Notifications périodiques][Notifications périodiques].

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données]. À cet effet, vous allez exécuter la procédure suivante :

1.  [Définition de l'API personnalisée][Définition de l'API personnalisée]
2.  [Mise à jour de l'application pour activer les notifications périodiques][Mise à jour de l'application pour activer les notifications périodiques]
3.  [Test de l'application][Test de l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez avoir terminé le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données].

## <a name="define-custom-api"></a>Définition de l'API personnalisée

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

    ![][0]

2.  Cliquez sur l’onglet **API**, puis sur **Create a custom API**.

    ![][1]

    La boîte de dialogue **Create a new custom API** s’affiche.

3.  Définissez **Autorisation GET** sur **Tout le monde**, tapez *tiles* dans **Nom API**, puis cliquez sur le bouton de vérification.

    ![][2]

    La nouvelle API est créée avec un accès en lecture (GET) public.

4.  Cliquez sur la nouvelle entrée tiles dans la table API.

    ![][3]

5.  Cliquez sur l'onglet **Script** et remplacez le code existant par les lignes suivantes :

        exports.get = function(request, response) {
            var wns = require('wns');
            var todoItems = request.service.tables.getTable('TodoItem');
            todoItems.where({
                complete: false
            }).read({
                success: sendResponse
            });

            function sendResponse(results) {
                var tileText = {
                    text1: "My todo list"
                };
                var i = 0;
                console.log(results)
                results.forEach(function(item) {
                    tileText["text" + (i + 2)] = item.text;
                    i++;
                });
                var xml = wns.createTileSquareText01(tileText);
                response.set('content-type', 'application/xml');
                response.send(200, xml);
            }
        };

    Ce code renvoie les trois premiers éléments non terminés de la table TodoItem, puis les charge dans un objet JSON transmis à la fonction **wns**.**createTileSquareText01**. Cette fonction renvoie le code XML de modèle de vignette suivant :

        <tile>
            <visual>
                <binding template="TileSquareText01">
                    <text id="1">My todo list</text>
                    <text id="2">Task 1</text>
                    <text id="3">Task 2</text>
                    <text id="4">Task 3</text>
                </binding>
            </visual>
        </tile>

    La fonction **exports.get** est utilisée parce que le client enverra une requête GET pour accéder au modèle de vignette.

    <div class="dev-callout"><b>Remarque</b>
    <p>Ce script d'API personnalis&eacute; utilise le <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">module wns</a> Node.js, qui est r&eacute;f&eacute;renc&eacute; en utilisant la fonction <strong>require</strong>. Ce module est diff&eacute;rent de l'<a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">objet wns</a> renvoy&eacute; par l'<a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554217.aspx">objet push</a>, lequel est utilis&eacute; pour envoyer des notifications push &agrave; partir de scripts serveur.</p>
</div>

Vous allez ensuite modifier l'application de démarrage rapide pour démarrer des notifications périodiques qui mettent à jour la vignette dynamique en demandant la nouvelle API personnalisée.

## <a name="update-app"></a><span class="short-header">Mise à jour de l'application </span>Mise à jour de l'application pour activer les notifications périodiques

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application de démarrage rapide du précédent didacticiel.

2.  Assurez-vous qu'un élément au moins est affiché. Si aucun élément n'est présent, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

3.  Dans Visual Studio, développez le dossier `\js` dans l'Explorateur de solutions, ouvrez le projet default.js, puis ajoutez les lignes de code suivantes après le code définissant la variable **client** :

        var notifications = Windows.UI.Notifications;
        var recurrence = notifications.PeriodicUpdateRecurrence.hour;
        var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

        notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

    Ce code active les notifications périodiques pour demander les données du modèle de vignette à la nouvelle API personnalisée **tiles**. Sélectionnez la valeur [PeriodicUpdateRecurrance] qui correspond le mieux à la fréquence de mise à jour de vos données.

## <a name="test-app"></a>Test de l'application

1.  Dans Visual Studio, appuyez sur la touche F5 pour réexécuter l'application.

    Les notifications périodiques sont alors activées.

2.  Accédez à l'écran d'accueil et recherchez la vignette dynamique correspondant à l'application. Comme vous pouvez le constater, les données de l'élément sont maintenant affichées dans la vignette.

    ![][4]

## Étapes suivantes

Maintenant que vous avez créé une notification périodique, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

-   [Prise en main des notifications push][Prise en main des notifications push]

    Les notifications périodiques sont gérées par Windows et obéissent à une planification prédéfinie. Les notifications Push peuvent être envoyées par le service mobile à la demande. Il peut s'agir de notifications toast, par vignette et brutes.

-   [Référence de script serveur Mobile Services][Référence de script serveur Mobile Services]

    Familiarisez-vous avec la création des API personnalisées.

  [Notifications périodiques]: http://msdn.microsoft.com/fr-fr/library/windows/apps/jj150587.aspx
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/started-with-data-js
  [Définition de l'API personnalisée]: #define-custom-api
  [Mise à jour de l'application pour activer les notifications périodiques]: #update-app
  [Test de l'application]: #test-app
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
  [2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
  [3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
  [4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png
  [Prise en main des notifications push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-js
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
