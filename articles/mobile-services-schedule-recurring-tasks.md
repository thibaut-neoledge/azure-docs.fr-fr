<!-- saved from url=(0022)http://internet.e-mail -->

Planification des travaux récurrents dans Mobile Services
=========================================================

Cette rubrique explique comment utiliser la fonctionnalité de planification de travaux dans le portail de gestion pour définir le code de script serveur exécuté selon une planification que vous définissez. Dans ce cas, le script effectue régulièrement une vérification auprès d'un service distant, Twitter en l'occurrence, et stocke les résultats dans une nouvelle table. D'autres tâches périodiques peuvent être planifiées, notamment les tâches suivantes :

-   Archivage des enregistrements de données anciens ou en double.
-   Demande et stockage de données externes, par exemple, des tweets, des entrées RSS et des informations d'emplacement.
-   Traitement ou redimensionnement des images stockées.

Ce didacticiel explique comment utiliser la planification des travaux pour créer un travail planifié qui demande des données de tweets à Twitter et stocke les tweets dans une nouvelle table Updates. La procédure comporte les étapes suivantes :

-   [Inscription pour l'accès à Twitter et stockage des informations d'identification](#get-oauth-credentials)
-   [Création de la nouvelle table Updates](#create-table)
-   [Création d'un nouveau travail planifié](#add-job)

Inscription pour l'accès aux API de Twitter v1.1 et stockage des informations d'identification
----------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

Création de la nouvelle table Updates
-------------------------------------

Ensuite, vous devez créer une nouvelle table pour y stocker les tweets.

1.  Dans le portail de gestion, cliquez sur l'onglet **Données** de votre service mobile, puis cliquez sur **+Créer**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png)

   	La boîte de dialogue **Create new table** s'affiche.

2.  Dans **Table name**, tapez *Updates*, puis cliquez sur le bouton de vérification.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png)

  	Une nouvelle table de stockage **Updates** est créée.

Création d'un nouveau travail planifié
--------------------------------------

Vous pouvez maintenant créer le travail planifié qui accède à Twitter et stocke les données des tweets dans la nouvelle table Updates.

1.  Cliquez sur l'onglet **Scheduler**, puis sur **+Créer**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png)

    >[WACOM.NOTE]Lorsque vous exécutez votre service mobile en mode *Gratuit*, vous ne pouvez exécuter qu'un seul travail planifié à la fois. Aux niveaux payants, vous pouvez exécuter jusqu'à dix travaux planifiés à la fois.

2.  Dans la boîte de dialogue du planificateur, entrez *getUpdates* pour le **Nom du travail**, définissez l'intervalle et les unités de planification, puis cliquez sur le bouton de vérification.
   
   	![](./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png)

   	Un nouveau travail nommé **getUpdates** est créé.

3.  Cliquez sur le travail que vous venez de créer, puis sur l'onglet **Script**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png) 

4.  Remplacez la fonction d'espace réservé **getUpdates** par le code suivant :

         var updatesTable = tables.getTable('Updates');
         var request = require('request');
         var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json
         q=%23mobileservices&result_type=recent";

         // Obtenir le module de configuration du service.
         var config = require('mobileservice-config');
            
         // Obtenez la clé et le code secret stockés du client Twitter. 
         var consumerKey = config.twitterConsumerKey,
             consumerSecret = config.twitterConsumerSecret
         // Obtenez le jeton d'accès Twitter à partir des paramètres de l'application.    
         var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
             accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;
            
         function getUpdates() {   
             // Vérifiez quel est le dernier tweet que nous avons stocké lors de la dernière exécution du travail
             // et demandez à Twitter de nous fournir uniquement les tweets plus récents
             appendLastTweetId(
                 twitterUrl, 
                 function twitterUrlReady(url){            
                     // Créez une nouvelle demande avec les informations d'identification OAuth.
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
         // Recherchez le plus grand ID de tweet (le plus récent) que nous avons déjà stocké
         // (le cas échéant) et demandez à Twitter de renvoyer uniquement les tweets
         // plus récents
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
             // Supprimez les retweets et les réponses
             return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
         }


   	Ce script appelle l'API de requête Twitter en utilisant les informations d'identification stockées pour demander les tweets récents qui contiennent la balise de hachage `#mobileservices`. Les tweets en double et les réponses sont supprimés des résultats avant que ces derniers ne soient stockés dans la table.

    > [WACOM.NOTE]Cet exemple part du principe que quelques lignes seulement sont insérées dans la table pendant chaque exécution planifiée. Si vous utilisez le mode Gratuit du service et qu'un grand nombre de lignes sont insérées dans une boucle, vous risquez de vous trouver à court de connexions. Dans ce cas, vous devez effectuer les insertions par lots. Pour plus d'informations, consultez la page [Insertions en bloc](/en-us/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts).

5.  Cliquez sur **Exécuter une fois** pour tester le script.

  	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png)

   	Le travail est exécuté et enregistré, tout en restant désactivé dans le planificateur.

1.  Cliquez sur le bouton Précédent, sur **Données**, sur la table **Updates**, sur **Parcourir** et vérifiez que les données Twitter ont été insérées dans la table.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png)

2.  Cliquez sur le bouton Précédent, sur **Scheduler**, sélectionnez **getUpdates**, puis cliquez sur **Activer**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png)

   	Cela permet d'exécuter le travail en fonction de la planification spécifiée, dans le cas présent, toutes les heures.

Félicitations, vous avez créé un nouveau travail planifié dans votre service mobile. Celui-ci sera exécuté conformément à la planification jusqu'à ce que vous la désactiviez ou la modifiiez.

Étapes suivantes
----------------

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.
