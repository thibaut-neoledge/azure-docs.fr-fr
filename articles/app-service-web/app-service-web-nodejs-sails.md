---
title: "Déploiement d’une application web Sails.js dans Azure App Service | Microsoft Docs"
description: "Découvrez comment déployer une application Node.js dans Azure App Service. Ce didacticiel vous explique comment déployer une application web Sails.js."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 8877ddc8-1476-45ae-9e7f-3c75917b4564
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 09ececc567c09ea4e0b77d4d37445b7c232de23c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---
# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Déployer une application web Sails.js dans Azure App Service
Ce didacticiel vous montre comment déployer une application Sails.js dans Azure App Service. Il permet également de comprendre comment configurer votre application Node.js à des fins d’exécution dans App Service.

Vous y gagnerez des compétences utiles telles que :

* la configuration d’une application Sails.js s’exécutant dans App Service ;
* le déploiement d’une application dans App Service à partir de la ligne de commande ;
* la lecture de journaux stderr et stdout pour résoudre les éventuels problèmes de déploiement ;
* le stockage de variables d’environnement en dehors du contrôle de code source ;
* l’accès aux variables d’environnement Azure à partir de votre application ;
* la connexion à une base de données (MongoDB).

Vous devriez avec une bonne connaissance de Sails.js. Ce didacticiel n’est pas destiné à résoudre les problèmes liés à l’exécution de Sail.js en général.

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](app-service-web-nodejs-sails-cli-nodejs.md) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager
- [Azure CLI 2.0](app-service-web-nodejs-sails.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="prerequisites"></a>Composants requis
* [Node.JS](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [Azure CLI 2.0](/cli/azure/install-az-cli2)
* Un compte Microsoft Azure Si vous n’avez pas de compte, vous pouvez [vous inscrire pour un essai gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Vous pouvez [essayer App Service](https://azure.microsoft.com/try/app-service/) sans compte Azure. Créez une application de base et expérimentez-la pendant une heure, sans carte de paiement et sans engagement.
> 
> 

## <a name="step-1-create-and-configure-a-sailsjs-app-locally"></a>Étape 1 : Créer et configurer une application Sails.js localement
Commencez par créer rapidement une application Sails.js par défaut dans votre environnement de développement en procédant comme suit :

1. Ouvrez le terminal de ligne de commande de votre choix et tapez la commande `CD` pointant vers un répertoire de travail.
2. Créez une application Sails.js et exécutez-la :

        sails new <app_name>
        cd <app_name>
        sails lift

    Vérifiez que vous pouvez accéder à la page d’accueil par défaut à l’adresse http://localhost:1377.

1. Ensuite, activez la journalisation pour Azure. Dans votre répertoire racine, créez un fichier nommé `iisnode.yml` et ajoutez les deux lignes suivantes :

        loggingEnabled: true
        logDirectory: iisnode

    La journalisation est maintenant activée pour le serveur [iisnode](https://github.com/tjanczuk/iisnode) qu’Azure App Service utilise pour exécuter des applications Node.js. 
    Pour plus d’informations sur la façon dont ceci fonctionne, consultez  [Guide pratique pour déboguer une application web Node.js dans Azure App Service](web-sites-nodejs-debug.md).

2. Ensuite, configurez l’application Sails.js pour utiliser des variables d’environnement Azure. Ouvrez config/env/production.js pour configurer votre environnement de production et définir `port` et `hookTimeout` :

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    La description de ces paramètres de configuration est disponible dans la  [documentation Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

4. Ensuite, codez en dur la version de Node.js que vous souhaitez utiliser. Dans package.json, ajoutez le code `engines` suivant pour définir la version Node.js voulue.

        "engines": {
            "node": "6.9.1"
        },

5. Enfin, initialisez un référentiel Git et validez vos fichiers. Dans la racine de l’application (où se trouve package.json), exécutez les commandes Git suivantes :

        git init
        git add .
        git commit -m "<your commit message>"

Votre code est prêt à être déployé. 

## <a name="step-2-create-an-azure-app-and-deploy-sailsjs"></a>Étape 2 : Créer une application Azure et déployer Sails.js

Ensuite, créez la ressource App Service dans Azure et déployez votre application Sails.js dans celle-ci.

1. connectez-vous au portail Azure :

        az login

    Suivez les instructions de l’invite pour poursuivre la connexion à un compte Microsoft associé à votre abonnement Azure.

3. Définissez l’utilisateur de déploiement pour App Service. Vous déploierez le code ultérieurement à l’aide de ces informations d’identification.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) avec un nom. Pour ce didacticiel node.js, il n’est pas vraiment nécessaire de vous familiariser avec cela.

        az group create --location "<location>" --name my-sailsjs-app-group

    Pour connaître les valeurs possibles que vous pouvez utiliser pour `<location>`, utilisez la commande CLI `az appservice list-locations`.

3. Créez un [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) « FREE » avec un nom. Pour ce didacticiel node.js, sachez que vous ne serez pas facturé pour les Web Apps utilisées dans le cadre de cette offre.

        az appservice plan create --name my-sailsjs-appservice-plan --resource-group my-sailsjs-app-group --sku FREE

4. Créez une application web avec un nom unique dans `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-sailsjs-app-group --plan my-sailsjs-appservice-plan

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Étape 3 : Configurer et déployer votre application Sails.js

1. Configurez le déploiement Git local pour votre nouvelle application web avec la commande suivante :

        az appservice web source-control config-local-git --name <app_name> --resource-group my-sailsjs-app-group

    Vous obtenez une sortie JSON similaire à ce qui suit, ce qui signifie que le référentiel Git distant est configuré :

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Ajoutez l’URL dans le fichier JSON en tant que Git distant pour votre référentiel local (appelé `azure` par souci de simplicité).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Déployez votre exemple de code dans le Git distant `azure`. Lorsque vous y êtes invité, utilisez les informations d’identification de déploiement que vous avez configurées précédemment.

        git push azure master

7. Pour terminer, lancez votre application Azure en direct dans le navigateur :

        az appservice web browse --name <app_name> --resource-group my-sailsjs-app-group

    Vous devez maintenant voir la même page d’accueil Sails.js que ci-dessous.

    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Résoudre les problèmes de votre déploiement
Si votre application Sails.js échoue pour une raison quelconque dans App Service, référez-vous aux journaux stderr pour résoudre le problème.
Pour plus d’informations, consultez [Guide pratique pour déboguer une application web Node.js dans Azure App Service](web-sites-nodejs-debug.md).
Si l’application a correctement démarré, le journal stdout doit afficher le message familier suivant :

                   .-..-.
    
       Sails              <|    .-..-.
       v0.12.11            |\
                          /|.\
                         / || \
                       ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
       __---___--___---___--___---___--___
     ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

Vous pouvez contrôler la granularité des journaux stdout dans le fichier [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) .

## <a name="connect-to-a-database-in-azure"></a>Se connecter à une base de données dans Azure
Pour vous connecter à une base de données dans Azure, créez la base de données de votre choix dans Azure, par exemple Azure SQL Database, MySQL, MongoDB, Cache (Redis) Azure, etc. et utilisez [l’adaptateur de banque de données](https://github.com/balderdashy/sails#compatibility) correspondant pour la connexion. Les étapes décrites dans cette section vous montrent comment se connecter à MongoDB à l’aide d’une base de données [Azure Cosmos DB](../documentdb/documentdb-protocol-mongodb.md), qui peut prendre en charge les connexions clientes à MongoDB.

1. [Créez un compte Cosmos DB prenant en charge le protocole MongoDB](../documentdb/documentdb-create-mongodb-account.md).
2. [Créez une collection et une base de données Cosmos DB](../documentdb/documentdb-create-collection.md). Le nom de la collection n’a pas d’importance, mais vous avez besoin du nom de la base de données lorsque vous vous connectez à partir de Sails.js.
3. [Recherchez les informations de connexion pour votre base de données Cosmos DB](../cosmos-db/connect-mongodb-account.md#a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize).
2. Dans votre terminal de ligne de commande, installez l’adaptateur MongoDB :

        npm install sails-mongo --save

3. Ouvrez config/connections.js et ajoutez l’objet de connexion suivant à la liste :

        docDbMongo: {
            adapter: 'sails-mongo',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost,
            port: process.env.dbport,
            database: process.env.dbname,
            ssl: true
        },

    > [!NOTE] 
    > L’option `ssl: true` est importante, car [Cosmos DB en a besoin](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 
    >
    >

4. Vous devez définir chaque variable d’environnement (`process.env.*`) dans App Service. Pour ce faire, exécutez les commandes suivantes à partir de votre terminal. Utilisez les informations de connexion pour Cosmos DB.

        az appservice web config appsettings update --settings dbuser="<database user>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbpassword="<database password>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbhost="<database hostname>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbport="<database port>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbname="<database name>" --name <app_name> --resource-group my-sailsjs-app-group

    Activer vos paramètres dans les paramètres de l’application Azure conserve les données sensibles hors de votre contrôle de source (Git). Ensuite, vous configurez votre environnement de développement pour utiliser les mêmes informations de connexion.
5. Ouvrez config/local.js et ajoutez l’objet connexion suivant :

        connections: {
            docDbMongo: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>",
                database: "<database name>",
                ssl: true
            },
        },

    Cette configuration remplace les paramètres de votre fichier config/connections.js pour l’environnement local. Ce fichier est exclu par le .gitignore par défaut dans votre projet, il n’est donc pas stocké dans Git. À présent, vous êtes en mesure de vous connecter à votre base de données Cosmos DB (MongoDB) depuis votre application web Azure et depuis votre environnement de développement local.
6. Ouvrez config/env/production.js pour configurer votre environnement de production et ajoutez l’objet `models` suivant :

        models: {
            connection: 'docDbMongo',
            migrate: 'safe'
        },
7. Ouvrez config/env/development.js pour configurer votre environnement de développement et ajoutez l’objet `models` suivant :

        models: {
            connection: 'docDbMongo',
            migrate: 'alter'
        },

    `migrate: 'alter'` vous permet d’utiliser les fonctionnalités de migration de base de données pour créer et mettre à jour facilement des collections de bases de données ou des tables. Cependant, `migrate: 'safe'` est utilisé pour votre environnement Azure (production), car Sails.js ne vous permet pas d’utiliser `migrate: 'alter'` dans un environnement de production (voir la  [documentation Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).
8. À partir du terminal, [générez](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) une [API blueprint](http://sailsjs.org/documentation/concepts/blueprints) Sails.js comme vous y êtes habitué, puis exécutez `sails lift` pour créer la base de données avec la de base de données de migration Sails.js. Par exemple :

         sails generate api mywidget
         sails lift

    Le modèle `mywidget` généré par cette commande est vide, mais nous pouvons l’utiliser pour montrer que nous disposons d’une connectivité de base de données.
    Lorsque vous exécutez `sails lift`, il crée les collections et les tables manquantes pour les modèles que votre application utilise.
9. À présent, accédez au modèle d’API que vous venez de créer dans le navigateur. Par exemple :

        http://localhost:1337/mywidget/create

    L’API doit retourner l’entrée créée dans la fenêtre du navigateur, ce qui signifie que votre collection a été créée.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. Maintenant, envoyez vos modifications dans Azure et accédez à votre application pour vérifier qu’elle fonctionne toujours.

         git add .
         git commit -m "<your commit message>"
         git push azure master
         az appservice web browse --name <app_name> --resource-group my-sailsjs-app-group

11. Accédez au modèle d’API de votre application web Azure. Par exemple :

         http://<appname>.azurewebsites.net/mywidget/create

     Si l’API renvoie une autre nouvelle entrée, votre application web Azure parle à votre base de données Cosmos DB (MongoDB).

## <a name="more-resources"></a>Autres ressources
* [Prise en main des applications web Node.js dans Azure App Service](app-service-web-get-started-nodejs.md)
* [Utilisation de modules Node.js avec des applications Azure](../nodejs-use-node-modules-azure-apps.md)

