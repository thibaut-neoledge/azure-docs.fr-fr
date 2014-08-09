<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="Node.js" title="How to Send Email Using SendGrid from Node.js" authors="" solutions="" manager="" editor="" />

Envoi de courrier électronique à l'aide de SendGrid depuis Node.js
==================================================================

Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en utilisant l'API Node.js. Les scénarios traités incluent la **construction** et l'**envoi de courriers électroniques**, l'**ajout de pièces jointes**, l'**utilisation de filtres**, et la **mise à jour de propriétés**. Pour plus d'informations sur SendGrid et sur l'envoi de courrier électronique, consultez la section [Étapes suivantes](http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/blob-storage/#next-steps).

Sommaire
--------

-   [Définition du service de messagerie SendGrid](#whatis)
-   [Création d'un compte SendGrid](#createaccount)
-   [Référencement du module Node.js SendGrid](#reference)
-   [Création d'un message électronique](#createemail)
-   [Envoi d'un message électronique](#sendemail)
-   [Ajout d'une pièce jointe](#addattachment)
-   [Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse](#usefilters)
-   [Mise à jour des propriétés de messagerie électronique](#updateproperties)
-   [Utilisation de services SendGrid supplémentaires](#useservices)
-   [Étapes suivantes](#nextsteps)

Présentation du service de messagerie électronique SendGrid
-----------------------------------------------------------

SendGrid est un [service de messagerie dans le cloud](http://sendgrid.com/solutions) qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle](http://sendgrid.com/transactional-email), d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation de SendGrid :

-   Envoi automatique d'accusés de réception aux clients
-   Administration de listes de distribution pour un envoi mensuel de prospectus électroniques et d'offres spéciales aux clients
-   Collecte de mesures en temps réel concernant des éléments tels que les messages électroniques bloqués et la réactivité vis-à-vis des clients
-   Génération de rapports pour identifier les tendances
-   Transfert des demandes des clients
-   Notifications par courriers électroniques depuis votre application

Pour plus d'informations, consultez la page <http://sendgrid.com>.

Création d'un compte SendGrid
-----------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Référencement du module Node.js SendGrid
----------------------------------------

Le module SendGrid pour Node.js peut être installé via le gestionnaire de package node (npm) à l'aide de la commande suivante :

    npm install sendgrid

Après l'installation, vous pouvez exiger la présence du module dans votre application à l'aide du code suivant :

    var SendGrid = require('sendgrid')

Le module SendGrid exporte les fonctions **SendGrid** et **Email**. **SendGrid** est chargé d'envoyer le courrier électronique via SMTP ou l'API Web, alors que **Email** permet d'encapsuler un message électronique.

Création d'un message électronique
----------------------------------

La création d'un message électronique à l'aide du module SendGrid consiste en premier lieu à créer un message électronique à l'aide de la fonction Email, puis à l'envoyer à l'aide de la fonction SendGrid. L'exemple suivant illustre la création d'un message à l'aide de la fonction Email :

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text     : 'This is a sample email message.'
    });

Vous pouvez également spécifier un message HTML pour les clients qui le prennent en charge en définissant la propriété html. Par exemple :

    html: This is a sample <b>HTML<b> email message.

Définir les propriétés text et html offre une solution de repli intéressante pour le contenu textuel dans le cas des clients qui ne peuvent pas prendre en charge les messages HTML.

Pour plus d'informations sur l'ensemble des propriétés prises en charge par la fonction Email, consultez la page [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

Envoi d'un message électronique
-------------------------------

Une fois le message électronique créé à l'aide de la fonction Email, vous pouvez l'envoyer via SMTP ou l'API Web fournie par SendGrid. Pour plus d'informations sur les avantages et les différences de chaque API, consultez la page [SMTP et API Web](http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/) dans la documentation SendGrid.

Avant de pouvoir utiliser l'API SMTP ou l'API Web, vous devez initialiser la fonction SendGrid en spécifiant l'utilisateur (user) et la clé (key) de votre compte SendGrid comme suit :

    var sender = new SendGrid.SendGrid('user','key');

Le message peut maintenant être envoyé via SMTP ou l'API Web. Les appels sont pratiquement identiques, avec la transmission du message électronique et d'une fonction de rappel facultative ; le rappel sert à déterminer la réussite ou l'échec de l'opération. Les exemples suivants montrent comment envoyer un message à l'aide de SMTP et de l'API Web.

### SMTP

    sender.smtp(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

### API Web

    sender.send(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

**Remarque**

Si les exemples précédents font intervenir un objet de messagerie électronique et une fonction de rappel, vous pouvez également appeler directement les fonctions send et smtp en spécifiant directement les propriétés de message électronique. Par exemple :

``` {.prettyprint}
sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text     : 'This is a sample email message.'
});
```

Ajout d'une pièce jointe
------------------------

Il est possible d'ajouter des pièces jointes à un message en spécifiant les noms de fichiers et les chemins d'accès dans la propriété **files**. L'exemple suivant illustre l'envoi d'une pièce jointe :

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text     : 'This is a sample email message.'
        files: {
            'file1.txt': __dirname + '/file1.txt',
            'image.jpg': __dirname + '/image.jpg'
        }
    });

**Remarque**

Lorsque la propriété **files** est utilisée, le fichier doit être accessible via [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Si le fichier que vous souhaitez joindre est hébergé dans Azure Storage, par exemple dans un conteneur d'objets blob, vous devez d'abord copier le fichier dans un stockage local ou un lecteur Azure avant de pouvoir l'envoyer sous forme de pièce jointe à l'aide de la propriété **files**.

Utilisation de filtres pour activer les pieds de page, le suivi et Twitter
--------------------------------------------------------------------------

SendGrid offre d'autres fonctionnalités de messagerie grâce à l'utilisation de filtres. Il s'agit de paramètres que vous pouvez ajouter à un message électronique pour activer des fonctionnalités spécifiques telles que le suivi des clics, Google Analytics, le suivi d'abonnement, etc. Pour obtenir une liste exhaustive des filtres, consultez la page [Paramètres de filtre](http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/).

Il est possible d'appliquer des filtres à un message par l'intermédiaire de la propriété **filters**. Chaque filtre est spécifié par un hachage contenant des paramètres propres au filtre. Les exemples suivants montrent les filtres de pied de page, de suivi des clics et de Twitter :

### Pied de page

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text     : 'This is a sample email message.'
        filters: {
            'footer': {
                'settings': {
                    'enable': 1,
                    'text/plain': 'This is a text footer.'
                }
            }
        }
    });

### Suivi des clics

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text     : 'This is a sample email message.'
        filters: {
            'clicktrack': {
                'settings': {
                    'enable': 1
                }
            }
        }
    });

### Twitter

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text     : 'This is a sample email message.'
        filters: {
            'twitter': {
                'settings': {
                    'enable': 1,
                    'username': 'twitter_username',
                    'password': 'twitter_password'
                }
            }
        }
    });

Mise à jour des propriétés de messagerie électronique
-----------------------------------------------------

Vous pouvez remplacer certaines propriétés de message électronique en utilisant **set*Property*** ou en ajouter en utilisant **add*Property***. Par exemple, vous pouvez ajouter des destinataires supplémentaires en utilisant

    email.addTo('jeff@contoso.com');

ou définir un filtre en utilisant

    email.setFilterSetting({
      'footer': {
        'setting': {
          'enable': 1,
          'text/plain': 'This is a footer.'
        }
      }
    });

Pour plus d'informations, consultez la page [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

Utilisation de services SendGrid supplémentaires
------------------------------------------------

SendGrid propose des API Web qui peuvent vous aider à tirer parti de fonctionnalités SendGrid supplémentaires à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API SendGrid](http://docs.sendgrid.com/documentation/api/).

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du service de messagerie SendGrid, consultez ces liens pour en savoir plus.

-   Référentiel du module Node.js SendGrid : [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs)
-   Documentation de l'API SendGrid : <http://docs.sendgrid.com/documentation/api/>
-   Offres spéciales SendGrid pour les clients Azure : <http://sendgrid.com/azure.html>

