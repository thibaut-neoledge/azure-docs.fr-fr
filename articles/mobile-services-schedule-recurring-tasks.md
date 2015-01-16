<properties urlDisplayName="Schedule Backend Tasks" pageTitle="Planification de tâches de serveur principal avec le planificateur - Mobile Services" metaKeywords="" description="Azure Mobile Services Scheduler permet de planifier des tâches pour votre application mobile." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Planification des travaux récurrents dans Mobile Services 

<div class="dev-center-tutorial-subselector">
	<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET backend">Serveur principal .NET</a> | <a href="/fr-fr/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript backend" class="current">Serveur principal JavaScript</a>
</div>
 
Cette rubrique explique comment utiliser la fonctionnalité de planification de travaux dans le portail de gestion pour définir le code de script serveur exécuté selon une planification que vous définissez. Dans ce cas, le script effectue régulièrement une vérification auprès d'un service distant, Twitter en l'occurrence, et stocke les résultats dans une nouvelle table. D'autres tâches périodiques peuvent être planifiées, notamment les tâches suivantes :

+ Archivage des enregistrements de données anciens ou en double.
+ Demande et stockage de données externes, par exemple, des tweets, des entrées RSS et des informations d'emplacement.
+ Traitement ou redimensionnement des images stockées.

Ce didacticiel explique comment utiliser la planification des travaux pour créer un travail planifié qui demande des données de tweets à Twitter et stocke les tweets dans une nouvelle table Updates. La procédure comporte les étapes suivantes :

+ [Inscription pour l'accès à Twitter et stockage des informations d'identification]
+ [Création de la nouvelle table Updates]
+ [Création d'un nouveau travail planifié]

## <a name="get-oauth-credentials"></a>Inscription pour l'accès aux API de Twitter v1.1 et stockage des informations d'identification

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

## <a name="create-table"></a>Création de la nouvelle table Updates

Ensuite, vous devez créer une nouvelle table pour y stocker les tweets.

2. Dans le portail de gestion, cliquez sur l'onglet **Données** de votre service mobile, puis cliquez sur **+Créer**.

   	![][2]

   	La boîte de dialogue **Create new table** s'ouvre.

3. Dans **Nom de la table**, tapez _Updates_, puis cliquez sur le bouton de vérification.

   	![][3]

  	Une nouvelle table de stockage **Updates** est créée. 

## <a name="add-job"></a>Création d'un nouveau travail planifié  

Vous pouvez maintenant créer le travail planifié qui accède à Twitter et stocke les données des tweets dans la nouvelle table Updates.

2. Cliquez sur l'onglet **Scheduler**, puis sur **+Créer**. 

   	![][4]

    >[WACOM.NOTE]Lorsque vous exécutez votre service mobile en mode <em>Gratuit</em>, vous ne pouvez exécuter qu'un seul travail planifié à la fois. Aux niveaux payants, vous pouvez exécuter jusqu'à dix travaux planifiés à la fois.

3. Dans la boîte de dialogue du planificateur, entrez _getUpdates_ pour **Nom du travail**, définissez l'intervalle et les unités de planification, puis cliquez sur le bouton de vérification. 
   
   	![][5]

   	Un nouveau travail nommé **getUpdates** est créé. 

4. Cliquez sur le travail que vous venez de créer, puis sur l'onglet **Script**.

   	![][6] 

5. Remplacez la fonction d'espace réservé **getUpdates** par le code suivant :

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

    >[WACOM.NOTE]Cet exemple part du principe que quelques lignes seulement sont insérées dans la table pendant chaque exécution planifiée. Si vous utilisez le mode Gratuit du service et qu'un grand nombre de lignes sont insérées dans une boucle, vous risquez de vous trouver à court de connexions. Dans ce cas, vous devez effectuer les insertions par lots. Pour plus d'informations, consultez la page <a href="/fr-fr/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts"> Insertions en bloc</a>.

6. Cliquez sur **Exécuter une fois** pour tester le script. 

  	![][7]
       
   	Le travail est exécuté et enregistré, tout en restant désactivé dans le planificateur.

7. Cliquez sur le bouton Précédent, sur **Données**, sur la table **Updates**, sur **Parcourir** et vérifiez que les données Twitter ont été insérées dans la table.

   	![][8]

8. Cliquez sur le bouton Précédent, sur **Scheduler**, sélectionnez **getUpdates**, puis cliquez sur **Activer**.

   	![][9]

   	Cela permet d'exécuter le travail en fonction de la planification spécifiée, dans le cas présent, toutes les heures.

Félicitations, vous avez créé un travail planifié dans votre service mobile. Celui-ci sera exécuté conformément à la planification jusqu'à ce que vous la désactiviez ou la modifiiez.

## <a name="nextsteps"> </a>Étapes suivantes

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

<!-- Anchors. -->
[Inscription pour l'accès à Twitter et stockage des informations d'identification]: #get-oauth-credentials
[Création de la nouvelle table Updates]: #create-table
[Création d'un nouveau travail planifié]: #add-job
[Étapes suivantes]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-schedule-recurring-tasks/mobile-twitter-my-apps.png
[1]: ./media/mobile-services-schedule-recurring-tasks/mobile-twitter-app-secrets.png
[2]: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
[3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
[4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[5]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
[6]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
[7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
[8]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
[9]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
[10]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-app-settings.png
[11]: ./media/mobile-services-schedule-recurring-tasks/mobile-identity-tab-twitter-only.png

<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Inscription des applications pour l'authentification Twitter avec Mobile Services]: /fr-fr/develop/mobile/how-to-guides/register-for-twitter-authentication
[Développeurs Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Paramètres de l'application]: http://msdn.microsoft.com/fr-fr/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
