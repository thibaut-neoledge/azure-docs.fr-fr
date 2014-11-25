<properties linkid="develop-mobile-tutorials-send-email-with-sendgrid" urlDisplayName="Send Email Using SendGrid" pageTitle="Send email using SendGrid - Azure Mobile Services" metaKeywords="Azure SendGrid, SendGrid service, Azure emailing, mobile services email" description="Learn how to use the SendGrid service to send email from your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="Mobile" title="Send email from Mobile Services with SendGrid" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Envoi de courrier électronique à partir de Mobile Services avec SendGrid

Cette rubrique vous montre comment ajouter une fonctionnalité de messagerie électronique à votre service mobile. Dans ce didacticiel, vous allez ajouter des scripts côté serveur pour envoyer du courrier électronique à l'aide de SendGrid. Une fois que vous aurez terminé, votre service mobile enverra un message électronique chaque fois qu'un enregistrement sera inséré.

SendGrid est un [service de messagerie dans le cloud][service de messagerie dans le cloud] qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle][remise de courrier électronique transactionnelle], d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Pour plus d'informations, consultez la page <http://sendgrid.com>.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer la fonctionnalité de messagerie électronique :

1.  [Création d'un compte SendGrid][Création d'un compte SendGrid]
2.  [Ajout d'un script pour envoyer du courrier électronique][Ajout d'un script pour envoyer du courrier électronique]
3.  [Insertion de données pour recevoir du courrier électronique][Insertion de données pour recevoir du courrier électronique]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

## <a name="sign-up"></a><span class="short-header">Création d'un compte</span>Création d'un compte SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="add-script"></a><span class="short-header">Inscription d'un script</span>Inscription d'un nouveau script qui envoie des messages électroniques

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur le service mobile.

2.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![][0]

3.  Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.

    ![][1]

    La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

4.  Remplacez la fonction d'insertion par le code suivant :

        var SendGrid = require('sendgrid').SendGrid;

        function insert(item, user, request) {    
            request.execute({
                success: function() {
                    // After the record has been inserted, send the response immediately to the client
                    request.respond();
                    // Send the email in the background
                    sendEmail(item);
                }
            });

            function sendEmail(item) {
                var sendgrid = new SendGrid('**username**', '**password**');       

                sendgrid.send({
                    to: '**email-address**',
                    from: '**from-address**',
                    subject: 'New to-do item',
                    text: 'A new to-do was added: ' + item.text
                }, function(success, message) {
                    // If the email failed to send, log it as an error so we can investigate
                    if (!success) {
                        console.error(message);
                    }
                });
            }
        }

5.  Remplacez les espaces réservés par les valeurs appropriées dans le script ci-dessus :

    -   ***username* et *password*** : informations d'identification SendGrid que vous avez identifiées dans la section [Création d'un compte SendGrid][Création d'un compte SendGrid].

    -   ***email-address*** : adresse à laquelle le message électronique est envoyé. Dans une application réelle, vous pouvez utiliser des tables pour stocker et récupérer les adresses électroniques. Au moment de tester votre application, utilisez uniquement votre propre adresse électronique.

    -   ***from-address*** : adresse d'origine du message électronique. Envisagez d'utiliser une adresse de domaine inscrit appartenant à votre organisation.

   	 <div class="dev-callout"><b>Remarque</b><br /> <p>Si vous n'avez pas de domaine inscrit, vous pouvez utiliser le domaine de votre service mobile au format <strong>notifications@<i>votre-service-mobile</i>.azure-mobile.net</strong>. Toutefois, les messages envoy&eacute;s au domaine de votre service mobile sont ignor&eacute;s.</p></div>

6.  Cliquez sur le bouton **Enregistrer**. Vous avez maintenant configuré un script pour qu'un message électronique soit envoyé chaque fois qu'un enregistrement est inséré dans la table **TodoItem**.

## <a name="insert-data"></a><span class="short-header">Insertion de données de test</span>Insertion de données de test pour recevoir du courrier électronique

1.  Dans le projet d'application cliente, exécutez l'application de démarrage rapide.

    Cette rubrique présente la version Windows Store du démarrage rapide.

2.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][2]

3.  Vous recevez un message électronique tel que celui figurant dans la notification ci-dessous.

    ![][3]

    Félicitations, vous avez bien configuré votre service mobile pour envoyer du courrier électronique à l'aide de SendGrid.

## <a name="nextsteps"> </a>Étapes suivantes

Maintenant que vous avez vu à quel point il est facile d'utiliser le service de messagerie électronique SendGrid avec Mobile Services, suivez
ces liens pour en savoir plus sur SendGrid.

-   Documentation API SendGrid :
    <http://docs.sendgrid.com/documentation/api/>
-   Offres spéciales SendGrid pour les clients Azure :
    <http://sendgrid.com/azure.html>

  [service de messagerie dans le cloud]: http://sendgrid.com/solutions
  [remise de courrier électronique transactionnelle]: http://sendgrid.com/transactional-email
  [Création d'un compte SendGrid]: #sign-up
  [Ajout d'un script pour envoyer du courrier électronique]: #add-script
  [Insertion de données pour recevoir du courrier électronique]: #insert-data
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png
  [1]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png
  [2]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png
  [3]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png
