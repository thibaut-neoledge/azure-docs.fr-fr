---
title: Utilisation du service de messagerie électronique SendGrid (Node.js) | Microsoft Docs
description: Découvrez comment envoyer un courrier électronique avec le service de messagerie SendGrid dans Azure. Exemples de code écrits à l'aide de l'API Node.js.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''

ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre

---
# Envoi de courrier électronique à l'aide de SendGrid depuis Node.js
Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en utilisant l'API Node.js. Les scénarios traités incluent la **construction** et l'**envoi de courriers électroniques**, l'**ajout de pièces jointes**, l'**utilisation de filtres**, et la **mise à jour de propriétés**. Pour plus d'informations sur SendGrid et sur l'envoi de courrier électronique, consultez la section [Étapes suivantes](#next-steps).

## Définition du service de messagerie SendGrid
SendGrid est un [service de messagerie dans le cloud](https://sendgrid.com/email-solutions) qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle](https://sendgrid.com/transactional-email), d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation de SendGrid :

* Envoi automatique d'accusés de réception aux clients
* Administration de listes de distribution pour un envoi mensuel de prospectus électroniques et d'offres spéciales aux clients
* Collecte de mesures en temps réel concernant des éléments tels que les messages électroniques bloqués et la réactivité vis-à-vis des clients
* Génération de rapports pour identifier les tendances
* Transfert des demandes des clients
* Notifications par courriers électroniques depuis votre application

Pour plus d'informations, consultez la page [https://sendgrid.com](https://sendgrid.com).

## Création d'un compte SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## Référencement du module Node.js SendGrid
Le module SendGrid pour Node.js peut être installé via le gestionnaire de package node (npm) à l'aide de la commande suivante :

    npm install sendgrid

Après l'installation, vous pouvez exiger la présence du module dans votre application à l'aide du code suivant :

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

Le module SendGrid exporte les fonctions **SendGrid** et **Email**. **SendGrid** est chargé d'envoyer le courrier électronique via l'API Web, alors que **Email** permet d'encapsuler un message électronique.

## Création d'un message électronique
La création d'un message électronique à l'aide du module SendGrid consiste en premier lieu à créer un message électronique à l'aide de la fonction Email, puis à l'envoyer à l'aide de la fonction SendGrid. L'exemple suivant illustre la création d'un message à l'aide de la fonction Email :

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Vous pouvez également spécifier un message HTML pour les clients qui le prennent en charge en définissant la propriété html. Par exemple :

    html: This is a sample <b>HTML<b> email message.

Définir les propriétés text et html offre une solution de repli intéressante pour le contenu textuel dans le cas des clients qui ne peuvent pas prendre en charge les messages HTML.

Pour plus d'informations sur l'ensemble des propriétés prises en charge par la fonction Email, consultez la page [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

## Envoi d'un message électronique
Une fois le message électronique créé à l'aide de la fonction Email, vous pouvez l'envoyer via l'API Web fournie par SendGrid.

### API Web
    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [!NOTE]
> Si les exemples précédents font intervenir un objet de messagerie électronique et une fonction de rappel, vous pouvez également appeler directement la fonction send en spécifiant directement les propriétés de message électronique. Par exemple :
> 
> `````
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> `````
> 
> 

## Ajout d'une pièce jointe
Il est possible d'ajouter des pièces jointes à un message en spécifiant les noms de fichiers et les chemins d'accès dans la propriété **files**. L'exemple suivant illustre l'envoi d'une pièce jointe :

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [!NOTE]
> Lorsque la propriété **files** est utilisée, le fichier doit être accessible via [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Si le fichier que vous souhaitez joindre est hébergé dans Azure Storage, par exemple dans un conteneur d'objets blob, vous devez d'abord copier le fichier dans un stockage local ou un lecteur Azure avant de pouvoir l'envoyer sous forme de pièce jointe à l'aide de la propriété **files**.
> 
> 

## Utilisation de filtres pour activer les pieds de page et le suivi
SendGrid offre des fonctionnalités de messagerie électronique supplémentaires grâce à l'utilisation des filtres. Il s'agit de paramètres que vous pouvez ajouter à un message électronique pour activer des fonctionnalités spécifiques telles que le suivi des clics, Google Analytics, le suivi d'abonnement, etc. Pour obtenir une liste exhaustive des filtres, consultez la page [Paramètres de filtre](https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html).

Il est possible d'appliquer des filtres à un message par l'intermédiaire de la propriété **filters**. Chaque filtre est spécifié par un hachage contenant des paramètres propres au filtre. Les exemples suivants montrent les filtres de pied de page et de suivi des clics :

### Pied de page
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### Suivi des clics
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });

    sendgrid.send(email);

## Mise à jour des propriétés de messagerie électronique
Certaines propriétés de courrier électronique peuvent être remplacées à l'aide de **set*Property*** ou ajoutées à l'aide de **add*Property***. Par exemple, vous pouvez ajouter des destinataires supplémentaires en utilisant

    email.addTo('jeff@contoso.com');

ou définir un filtre en utilisant

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Pour plus d'informations, consultez la page [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

## Utilisation de services SendGrid supplémentaires
SendGrid propose des API web qui peuvent vous aider à tirer parti de fonctionnalités SendGrid supplémentaires à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API SendGrid](https://sendgrid.com/docs).

## Étapes suivantes
Maintenant que vous avez appris les bases du service de messagerie SendGrid, consultez ces liens pour en savoir plus.

* Référentiel du module Node.js SendGrid : [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs)
* Documentation de l'API SendGrid : <https://sendgrid.com/docs>
* Offre spéciale SendGrid pour les clients Azure : [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[offre spéciale]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Paramètres de filtre]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[documentation de l’API SendGrid]: https://sendgrid.com/docs
[service de messagerie dans le cloud]: https://sendgrid.com/email-solutions
[remise de courrier électronique transactionnelle]: https://sendgrid.com/transactional-email

<!---HONumber=AcomDC_0107_2016-->