<properties linkid="develop-mobile-tutorials-schedule-backend-tasks" urlDisplayName="Schedule Backend Tasks" pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Planification des travaux récurrents dans Mobile Services

<div class="dev-center-tutorial-subselector">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET backend">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript backend" class="current">JavaScript backend</a>
</div>

Cette rubrique explique comment utiliser la fonctionnalité de planification de travaux dans le portail de gestion pour définir le code de script serveur exécuté selon une planification que vous définissez. Dans ce cas, le script effectue régulièrement une vérification auprès d'un service distant, Twitter en l'occurrence, et stocke les résultats dans une nouvelle table. D'autres tâches périodiques peuvent être planifiées, notamment les tâches suivantes :

-   Archivage des enregistrements de données anciens ou en double.
-   Demande et stockage de données externes, par exemple, des tweets, des entrées RSS et des informations d'emplacement.
-   Traitement ou redimensionnement des images stockées.

Ce didacticiel explique comment utiliser la planification des travaux pour créer un travail planifié qui demande des données de tweets à Twitter et stocke les tweets dans une nouvelle table Updates. La procédure comporte les étapes suivantes :

-   [Inscription pour l'accès à Twitter et stockage des informations d'identification][Inscription pour l'accès à Twitter et stockage des informations d'identification]
-   [Création de la nouvelle table Updates][Création de la nouvelle table Updates]
-   [Création d'un nouveau travail planifié][Création d'un nouveau travail planifié]

## <a name="get-oauth-credentials"></a>Inscription pour l'accès aux API de Twitter v1.1 et stockage des informations d'identification

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

## <a name="create-table"></a>Création de la nouvelle table Updates

Ensuite, vous devez créer une nouvelle table pour y stocker les tweets.

1.  Dans le portail de gestion, cliquez sur l'onglet **Données** de votre service mobile, puis cliquez sur **+Créer**.

    ![][0]

    La boîte de dialogue **Create new table** s’affiche.

2.  Dans **Table name**, tapez *Updates*, puis cliquez sur le bouton de vérification.

    ![][1]

    Une nouvelle table de stockage **Updates** est créée.

## <a name="add-job"></a>Création d'un nouveau travail planifié

Vous pouvez maintenant créer le travail planifié qui accède à Twitter et stocke les données des tweets dans la nouvelle table Updates.

1.  Cliquez sur l'onglet **Scheduler**, puis sur **+Créer**.

    ![][2]

    > [WACOM.NOTE]Lorsque vous exécutez votre service mobile en mode *Gratuit*, vous ne pouvez exécuter qu'un seul travail planifié à la fois. Aux niveaux payants, vous pouvez exécuter jusqu'à dix travaux planifiés à la fois.

2.  Dans la boîte de dialogue du planificateur, entrez *getUpdates* pour le **Nom du travail**, définissez l'intervalle et les unités de planification, puis cliquez sur le bouton de vérification.

    ![][3]

    Cette commande crée une tâche nommée **getUpdates**.

3.  Cliquez sur le travail que vous venez de créer, puis sur l'onglet **Script**.

    ![][4]

4.  Remplacez la fonction d'espace réservé **getUpdates** par le code suivant :

        var updatesTable = tables.getTable('Updates');
        var request = require('request');
        var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret. 
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.    
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

        function getUpdates() {   
            // Check what is the last tweet we stored when the job last ran
            // and ask Twitter to only give us more recent tweets
            appendLastTweetId(
                twitterUrl, 
                function twitterUrlReady(url){            
                    // Create a new request with OAuth credentials.
                    request.get({
                        url: url,                
                        oauth: {
                            consumer_key: consumerKey,
                            consumer_secret: consumerSecret,
                            token: accessToken,
                            token_secret: accessTokenSecret
                        }},
                        function (error, response, body) {
                        if (!error && response.statusCode == 200) {
                            var results = JSON.parse(body).statuses;
                            if(results){
                                console.log('Fetched ' + results.length + ' new results from Twitter');                       
                                results.forEach(function (tweet){
                                    if(!filterOutTweet(tweet)){
                                        var update = {
                                            twitterId: tweet.id,
                                            text: tweet.text,
                                            author: tweet.user.screen_name,
                                            date: tweet.created_at
                                        };
                                        updatesTable.insert(update);
                                    }
                                });
                            }            
                        } else { 
                            console.error('Could not contact Twitter');
                        }
                    });

                });
         }
        // Find the largest (most recent) tweet ID we have already stored
        // (if we have stored any) and ask Twitter to only return more
        // recent ones
        function appendLastTweetId(url, callback){
            updatesTable
            .orderByDescending('twitterId')
            .read({success: function readUpdates(updates){
                if(updates.length){
                    callback(url + '&since_id=' + (updates[0].twitterId + 1));           
                } else {
                    callback(url);
                }
            }});
        }

        function filterOutTweet(tweet){
            // Remove retweets and replies
            return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
        }

    Ce script appelle l'API de requête Twitter en utilisant les informations d'identification stockées pour demander les tweets récents qui contiennent la balise de hachage `#mobileservices`. Les tweets en double et les réponses sont supprimés des résultats avant que ces derniers ne soient stockés dans la table.

    > [WACOM.NOTE]Cet exemple part du principe que quelques lignes seulement sont insérées dans la table pendant chaque exécution planifiée. Si vous utilisez le mode Gratuit du service et qu'un grand nombre de lignes sont insérées dans une boucle, vous risquez de vous trouver à court de connexions. Dans ce cas, vous devez effectuer les insertions par lots. Pour plus d'informations, consultez la page [Insertions en bloc][Insertions en bloc].

5.  Cliquez sur **Exécuter une fois** pour tester le script.

    ![][5]

    Le travail est exécuté et enregistré, tout en restant désactivé dans le planificateur.

6.  Cliquez sur le bouton Précédent, sur **Données**, sur la table **Updates**, sur **Parcourir** et vérifiez que les données Twitter ont été insérées dans la table.

    ![][6]

7.  Cliquez sur le bouton Précédent, sur **Scheduler**, sélectionnez **getUpdates**, puis cliquez sur **Activer**.

    ![][7]

    Cela permet d'exécuter le travail en fonction de la planification spécifiée, dans le cas présent, toutes les heures.

Félicitations, vous avez créé un nouveau travail planifié dans votre service mobile. Celui-ci sera exécuté conformément à la planification jusqu'à ce que vous la désactiviez ou la modifiiez.

## <a name="nextsteps"> </a>Étapes suivantes

-   [Référence de script serveur Mobile Services][Référence de script serveur Mobile Services]
    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

  [Inscription pour l'accès à Twitter et stockage des informations d'identification]: #get-oauth-credentials
  [Création de la nouvelle table Updates]: #create-table
  [Création d'un nouveau travail planifié]: #add-job
  [0]: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
  [1]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
  [2]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
  [3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
  [4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
  [Insertions en bloc]: /fr-fr/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts
  [5]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
  [6]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
  [7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
