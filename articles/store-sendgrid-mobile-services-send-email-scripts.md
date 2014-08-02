<properties linkid="develop-mobile-tutorials-send-email-with-sendgrid" urlDisplayName="Send Email Using SendGrid" pageTitle="Send email using SendGrid - Azure Mobile Services" metaKeywords="Azure SendGrid, SendGrid service, Azure emailing, mobile services email" description="Learn how to use the SendGrid service to send email from your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="Mobile" title="Send email from Mobile Services with SendGrid" authors="" solutions="" manager="" editor="" />

Envoi de courrier électronique à partir de Mobile Services avec SendGrid
========================================================================

Cette rubrique vous montre comment ajouter une fonctionnalité de messagerie électronique à votre service mobile. Dans ce didacticiel, vous allez ajouter des scripts côté serveur pour envoyer du courrier électronique à l'aide de SendGrid. Une fois que vous aurez terminé, votre service mobile enverra un message électronique chaque fois qu'un enregistrement sera inséré.

SendGrid est un [service de messagerie dans le cloud](http://sendgrid.com/solutions) qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle](http://sendgrid.com/transactional-email), d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Pour plus d'informations, consultez la page <http://sendgrid.com>.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer la fonctionnalité de messagerie électronique :

1.  [Création d'un compte SendGrid](#sign-up)
2.  [Ajout d'un script pour envoyer du courrier électronique](#add-script)
3.  [Insertion de données pour recevoir du courrier électronique](#insert-data)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started).

Création d'un compteCréation d'un compte SendGrid
-------------------------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Inscription d'un scriptInscription d'un nouveau script qui envoie des messages électroniques
--------------------------------------------------------------------------------------------

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

2.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![](./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png)

3.  Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.

    ![](./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png)

    La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

4.  Remplacez la fonction d'insertion par le code suivant :

         var SendGrid = require('sendgrid').SendGrid;
            
         function insert(item, user, request) {    
             request.execute({
                 success: function() {
                     // Après avoir inséré l'enregistrement, envoyez la réponse immédiatement au client
                     request.respond();
                     // Envoyez le courrier électronique en arrière-plan
                     sendEmail(item);
                 }
             });

             function sendEmail(item) {
                 var sendgrid = new SendGrid('**username**', '**password**');       
                    
                 sendgrid.send({
                     to: '**email-address**',
                     from: '**from-address**',
                     subject: 'New to-do item',
                     text     : 'A new to-do was added: " + item.text,
                 }, function(success, message) {
                     // En cas d'échec de l'envoi du message, consignez-le en tant qu'erreur pour que nous puissions l'examiner
                     if (!success) {
                         console.error(message);
                     }
                 });
             }
         }

5.  Remplacez les espaces réservés par les valeurs appropriées dans le script ci-dessus :

    -   ***username* et *password*** : informations d'identification SendGrid que vous avez identifiées dans la section [Création d'un compte SendGrid](#sign-up).

    -   ***email-address*** : adresse à laquelle le message électronique est envoyé. Dans une application réelle, vous pouvez utiliser des tables pour stocker et récupérer les adresses électroniques. Au moment de tester votre application, utilisez uniquement votre propre adresse électronique.

    -   ***from-address*** : adresse d'origine du message électronique. Envisagez d'utiliser une adresse de domaine inscrit appartenant à votre organisation.

    **Remarque**

    Si vous n'avez pas de domaine inscrit, vous pouvez utiliser le domaine de votre service mobile au format **notifications@*votre-service-mobile*.azure-mobile.net**. Toutefois, les messages envoyés au domaine de votre service mobile sont ignorés.

6.  Cliquez sur le bouton **Enregistrer**. Vous avez maintenant configuré un script pour qu'un message électronique soit envoyé chaque fois qu'un enregistrement est inséré dans la table **TodoItem**.

Insertion de données de testInsertion de données de test pour recevoir du courrier électronique
-----------------------------------------------------------------------------------------------

1.  Dans le projet d'application cliente, exécutez l'application de démarrage rapide.

    Cette rubrique présente la version Windows Store du démarrage rapide.

2.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![](./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png)

3.  Vous recevez un message électronique tel que celui figurant dans la notification ci-dessous.

    ![](./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png)

    Félicitations, vous avez bien configuré votre service mobile pour envoyer du courrier électronique à l'aide de SendGrid.

Étapes suivantes
----------------

Maintenant que vous avez vu à quel point il est facile d'utiliser le service de messagerie électronique SendGrid avec Mobile Services, suivez ces liens pour en savoir plus sur SendGrid.

-   Documentation de l'API SendGrid : <http://docs.sendgrid.com/documentation/api/>
-   Offres spéciales SendGrid pour les clients Azure : <http://sendgrid.com/azure.html>

