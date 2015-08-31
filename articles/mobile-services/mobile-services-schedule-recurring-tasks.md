<properties 
	pageTitle="Planification des tâches du backend avec le planificateur | Microsoft Azure" 
	description="Azure Mobile Services Scheduler permet de planifier des tâches pour votre application mobile." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Planification des travaux récurrents dans Mobile Services 

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [(Any | Javascript)](mobile-services-schedule-recurring-tasks.md)
 
Cette rubrique explique comment utiliser la fonctionnalité de planification de travaux dans le portail de gestion pour définir le code de script serveur exécuté selon une planification que vous définissez. Dans ce cas, le script effectue régulièrement une vérification auprès d'un service distant, Twitter en l'occurrence, et stocke les résultats dans une nouvelle table. D'autres tâches périodiques peuvent être planifiées, notamment les tâches suivantes :

+ Archivage des enregistrements de données anciens ou en double.
+ Demande et stockage de données externes, par exemple, des tweets, des entrées RSS et des informations d'emplacement.
+ Traitement ou redimensionnement des images stockées.

Ce didacticiel explique comment utiliser la planification des travaux pour créer un travail planifié qui demande des données de tweets à Twitter et stocke les tweets dans une nouvelle table Updates. La procédure comporte les étapes suivantes :

##<a name="get-oauth-credentials"></a>S’inscrire pour l’accès aux API de Twitter v1.1 et stocker les informations d’identification

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

##<a name="create-table"></a>Créer la table Updates

Ensuite, vous devez créer une nouvelle table pour y stocker les tweets.

2. Dans le portail de gestion, cliquez sur l'onglet **Données** de votre service mobile, puis cliquez sur **+Créer**.

3. Dans **Table name**, tapez _Updates_, puis cliquez sur le bouton de vérification.

##<a name="add-job"></a>Créer une tâche planifiée  

Vous pouvez maintenant créer le travail planifié qui accède à Twitter et stocke les données des tweets dans la nouvelle table Updates.

2. Cliquez sur l'onglet **Scheduler**, puis sur **+Créer**. 

    >[AZURE.NOTE]Lorsque vous exécutez votre service mobile en mode <em>Gratuit</em>, vous ne pouvez exécuter qu'une tâche planifiée à la fois. Aux niveaux payants, vous pouvez exécuter jusqu'à dix travaux planifiés à la fois.

3. Dans la boîte de dialogue du planificateur, entrez _getUpdates_ pour le **Nom du travail**, définissez l'intervalle et les unités de planification, puis cliquez sur le bouton de vérification.

   	Cette commande crée une tâche nommée **getUpdates**.

4. Cliquez sur la tâche que vous venez de créer, cliquez sur l'onglet **Script** et remplacez la fonction de l'espace réservé **getUpdates** par le code suivant :

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


   	Ce script appelle l'API de requête Twitter en utilisant les informations d'identification stockées pour demander les tweets récents qui contiennent le balise de hachage `#mobileservices`. Les tweets en double et les réponses sont supprimés des résultats avant que ces derniers ne soient stockés dans la table.

    >[AZURE.NOTE]Cet exemple part du principe que quelques lignes seulement sont insérées dans la table pendant chaque exécution planifiée. Si vous utilisez le mode Gratuit du service et qu'un grand nombre de lignes sont insérées dans une boucle, vous risquez de vous trouver à court de connexions. Dans ce cas, vous devez effectuer les insertions par lots. Pour plus d’informations, consultez [Procédure : exécution d’insertions en bloc](mobile-services-how-to-use-server-scripts.md#bulk-inserts).

6. Cliquez sur **Exécuter une fois** pour tester le script.

   	Le travail est exécuté et enregistré, tout en restant désactivé dans le planificateur.

7. Cliquez sur le bouton Précédent, sur **Données**, sur la table **Updates**, sur **Parcourir** et vérifiez que les données Twitter ont été insérées dans la table.

8. Cliquez sur le bouton Précédent, sur **Scheduler**, sélectionnez **getUpdates**, puis cliquez sur **Activer**.

   	Cela permet d'exécuter le travail en fonction de la planification spécifiée, dans le cas présent, toutes les heures.

Félicitations, vous avez créé un nouveau travail planifié dans votre service mobile. Celui-ci sera exécuté conformément à la planification jusqu’à ce que vous la désactiviez ou la modifiiez.

## <a name="nextsteps"> </a>Voir aussi

* [Référence de script serveur Mobile Services] <br/>En savoir plus sur l’enregistrement et l’utilisation des scripts serveur.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Next steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Azure Management Portal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /develop/mobile/how-to-guides/register-for-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=August15_HO8-->