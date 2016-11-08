---
title: Déployer une application web Sails.js dans Azure App Service
description: Découvrez comment déployer une application Node.js dans Azure App Service. Ce didacticiel vous explique comment déployer une application web Sails.js.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 09/23/2016
ms.author: cephalin

---
# <a name="deploy-a-sails.js-web-app-to-azure-app-service"></a>Déployer une application web Sails.js dans Azure App Service
Ce didacticiel vous montre comment déployer une application Sails.js dans Azure App Service. Il permet également de comprendre comment configurer votre application Node.js à des fins d’exécution dans App Service. 

Vous devriez avec une bonne connaissance de Sails.js. Ce didacticiel n’est pas destiné à résoudre les problèmes liés à l’exécution de Sail.js en général.

## <a name="prerequisites"></a>Composants requis
* [Node.JS](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [Interface de ligne de commande Azure](../xplat-cli-install.md)
* Un compte Microsoft Azure Si vous n’avez pas de compte, vous pouvez [vous inscrire pour un essai gratuit](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Pour voir Azure App Service en action avant de créer un compte Azure, accédez à [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). De là, vous pouvez créer tout de suite une première application temporaire dans App Service. Aucune carte de crédit ni aucun engagement ne sont nécessaires.
> 
> 

## <a name="step-1:-create-a-sails.js-app-locally"></a>Étape 1 : Créer une application Sails.js localement
Commencez par créer rapidement une application Sails.js par défaut dans votre environnement de développement en procédant comme suit :

1. Ouvrez le terminal de ligne de commande de votre choix et tapez la commande `CD` pointant vers un répertoire de travail.
2. Créez une application Sails.js et exécutez-la :
   
        sails new <appname>
        cd <appname>
        sails lift
   
    Vérifiez que vous pouvez accéder à la page d’accueil par défaut à l’adresse http://localhost:1377.

## <a name="step-2:-create-the-azure-app-resource"></a>Étape 2 : Créer la ressource d’application Azure
Créez ensuite la ressource App Service dans Azure. Vous y déploierez votre application Sails.js plus tard.

1. connectez-vous au portail Azure :
2. Dans le même terminal, passez en mode ASM et connectez-vous à Azure :
   
        azure config mode asm
        azure login
   
    Suivez les instructions de l’invite pour poursuivre la connexion à un compte Microsoft associé à votre abonnement Azure.
3. Vérifiez que vous êtes toujours dans le répertoire racine de votre projet Sails.js. Créez la ressource d’application d’App Service dans Azure avec un nom d’application unique à l’aide de la commande suivante. L’URL de votre application web est http://&lt;appname>.azurewebsites.net.
   
        azure site create --git <appname>
   
    Suivez les instructions pour sélectionner une région Azure vers laquelle effectuer le déploiement. Si vous n’avez jamais configuré les informations d’identification de déploiement Git/FTP pour votre abonnement Azure, vous êtes invité à les créer.
   
    Une fois que la ressource d’application App Service est créée :
   
   * L’application Sails.js est initialisée dans Git.
   * Votre dépôt local initialisé dans Git est connecté à la nouvelle application App Service en tant que Git distant, intelligemment nommé « azure ».
   * Un fichier iisnode.yml est créé dans votre répertoire racine. Ce fichier permet de configurer [iisnode](https://github.com/tjanczuk/iisnode), qu’App Service utilise pour exécuter des applications Node.js.

## <a name="step-3:-configure-and-deploy-your-sails.js-app"></a>Étape 3 : Configurer et déployer votre application Sails.js
 L’utilisation d’une application Sails.js dans App Service se compose de trois étapes principales :

* Configurez votre application pour qu’elle s’exécute dans App Service.
* Déployez-la dans App Service.
* Lisez les journaux stderr et stdout pour résoudre les éventuels problèmes de déploiement.

Procédez comme suit :

1. Ouvrez le nouveau fichier iisnode.yml dans votre répertoire racine et ajoutez les deux lignes suivantes :
   
        loggingEnabled: true
        logDirectory: iisnode
   
    La journalisation est maintenant activée pour iisnode. Pour connaître son fonctionnement, consultez  [Obtenir des journaux stdout et stderr à partir d’iisnode](app-service-web-nodejs-get-started.md#iisnodelog).
2. Ouvrez config/env/production.js pour configurer votre environnement de production et définir `port` et `hookTimeout` :
   
        module.exports = {
   
            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,
   
            ...
        };
   
    La description de ces paramètres de configuration est disponible dans la  [documentation Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).
   
    Vous devez ensuite vérifier que [Grunt](https://www.npmjs.com/package/grunt) est compatible avec les lecteurs réseau Azure. Les versions de Grunt antérieures à 1.0.0 utilisent un package [glob](https://www.npmjs.com/package/glob) obsolète (antérieur à 5.0.14), qui ne prend pas en charge les lecteurs réseau. 
3. Ouvrez package.json et modifiez la version `grunt` en `1.0.0` et supprimez tous les packages `grunt-*`. Votre propriété `dependencies` doit avoir l’aspect suivant :
   
        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },
4. Dans package.json, ajoutez le code `engines` suivant pour définir la version Node.js voulue.
   
        "engines": {
            "node": "6.6.0"
        },
5. Enregistrez vos modifications et testez-les pour vérifier que votre application continue de s’exécuter localement. Pour ce faire, supprimez le dossier `node_modules` , puis exécutez :
   
        npm install
        sails lift
6. Utilisez maintenant Git pour déployer votre application dans Azure :
   
        git add .
        git commit -m "<your commit message>"
        git push azure master
7. Pour terminer, lancez votre application Azure en direct dans le navigateur :
   
        azure site browse
   
    Vous devez maintenant voir la même page d’accueil Sails.js que ci-dessous.
   
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Résoudre les problèmes de votre déploiement
Si votre application Sails.js échoue pour une raison quelconque dans App Service, référez-vous aux journaux stderr pour résoudre le problème.
Pour plus d’informations, consultez [Obtenir des journaux stdout et stderr à partir d’iisnode](app-service-web-nodejs-sails.md#iisnodelog).
Si l’application a correctement démarré, le journal stdout doit afficher le message familier suivant :

                .-..-.

    Sails              <|    .-..-.
    v0.12.4             |\
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
Pour vous connecter à une base de données dans Azure, créez la base de données de votre choix dans Azure, par exemple Azure SQL Database, MySQL, MongoDB, Cache (Redis) Azure, etc. et utilisez [l’adaptateur de banque de données](https://github.com/balderdashy/sails#compatibility) correspondant pour la connexion. Les étapes de cette section montrent comment se connecter à une base de données MySQL dans Azure.

1. Suivez [ce didacticiel](../store-php-create-mysql-database.md) pour créer une base de données MySQL dans Azure.
2. Dans votre terminal de ligne de commande, installez l’adaptateur MySQL :
   
        npm install sails-mysql --save
3. Ouvrez config/connections.js et ajoutez l’objet de connexion suivant à la liste : 
   
        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },
4. Vous devez définir chaque variable d’environnement (`process.env.*`) dans App Service. Pour ce faire, exécutez les commandes suivantes à partir de votre terminal. Toutes les informations de connexion dont vous avez besoin figurent dans le portail Azure (voir [Connexion à la base de données MySQL](../store-php-create-mysql-database.md#connect)).
   
        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
   
    Activer vos paramètres dans les paramètres de l’application Azure conserve les données sensibles hors de votre contrôle de source (Git). Ensuite, vous configurez votre environnement de développement pour utiliser les mêmes informations de connexion.
5. Ouvrez config/local.js et ajoutez l’objet connexion suivant :
   
        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
   
    Cette configuration remplace les paramètres de votre fichier config/connections.js pour l’environnement local. Ce fichier est exclu par le .gitignore par défaut dans votre projet, il n’est donc pas stocké dans Git. À présent, vous êtes en mesure de vous connecter à votre base de données MySQL depuis votre application web Azure et depuis votre environnement de développement local.
6. Ouvrez config/env/production.js pour configurer votre environnement de production et ajoutez l’objet `models` suivant :
   
        models: {
            connection: 'mySql',
            migrate: 'safe'
        },
7. Ouvrez config/env/development.js pour configurer votre environnement de développement et ajoutez l’objet `models` suivant :
   
        models: {
            connection: 'mySql',
            migrate: 'alter'
        },
   
    `migrate: 'alter'` vous permet d’utiliser les fonctionnalités de migration de base de données pour créer et mettre à jour vos tables de base de données dans votre base de données MySQL en toute facilité. Cependant, `migrate: 'safe'` est utilisé pour votre environnement Azure (production), car Sails.js ne vous permet pas d’utiliser `migrate: 'alter'` dans un environnement de production (voir la  [documentation Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).
8. À partir du terminal, [générez](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) une [API blueprint](http://sailsjs.org/documentation/concepts/blueprints) Sails.js comme vous y êtes habitué, puis exécutez `sails lift` pour créer la base de données avec la de base de données de migration Sails.js. Par exemple :
   
         sails generate api mywidget
         sails lift
   
    Le modèle `mywidget` généré par cette commande est vide, mais nous pouvons l’utiliser pour montrer que nous disposons d’une connectivité de base de données.
    Lorsque vous exécutez `sails lift`, il crée les tables manquantes pour les modèles que votre application utilise.
9. À présent, accédez au modèle d’API que vous venez de créer dans le navigateur. Par exemple :
   
        http://localhost:1337/mywidget/create
   
    L’API doit retourner l’entrée créée dans la fenêtre du navigateur, ce qui signifie que votre base de données a été créée avec succès.
   
        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. Maintenant, envoyez vos modifications dans Azure et accédez à votre application pour vérifier qu’elle fonctionne toujours.
    
         git add .
         git commit -m "<your commit message>"
         git push azure master
         azure site browse
11. Accédez au modèle d’API de votre application web Azure. Par exemple :
    
         http://<appname>.azurewebsites.net/mywidget/create
    
     Si l’API renvoie une autre nouvelle entrée, puis votre application web Azure parle à votre base de données MySQL.

## <a name="more-resources"></a>Autres ressources
* [Prise en main des applications web Node.js dans Azure App Service](app-service-web-nodejs-get-started.md)
* [Utilisation de modules Node.js avec des applications Azure](../nodejs-use-node-modules-azure-apps.md)

<!--HONumber=Oct16_HO2-->


