<properties
	pageTitle="Déployer une application web Sails.js dans Azure App Service"
	description="Découvrez comment déployer une application Node.js dans Azure App Service. Ce didacticiel vous explique comment déployer une application web Sails.js."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="03/31/2016"
	ms.author="cephalin"/>

# Déployer une application web Sails.js dans Azure App Service

Ce didacticiel vous montre comment déployer une application Sails.js dans Azure App Service. Il permet également de comprendre comment configurer votre application Node.js à des fins d’exécution dans App Service.

## Composants requis

- Node.js. Des fichiers binaires d’installation sont disponibles [ici](https://nodejs.org/).
- Sails.js. Des instructions d’installation sont disponibles [ici](http://sailsjs.org/get-started).
- Bonne connaissance de Sails.js. Ce didacticiel n’est pas destiné à résoudre les problèmes liés à l’exécution de Sail.js en général.
- Git. Des fichiers binaires d’installation sont disponibles [ici](http://www.git-scm.com/downloads).
- Interface de ligne de commande Azure Des instructions d’installation sont disponibles [ici](../xplat-cli-install.md).
- Un compte Microsoft Azure Si vous ne possédez pas de compte, vous pouvez [vous inscrire à un essai gratuit](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Pour voir Azure App Service en action avant de créer un compte Azure, accédez à [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). De là, vous pouvez créer tout de suite une première application temporaire dans App Service. Aucune carte de crédit ni aucun engagement ne sont nécessaires.

## Étape 1 : Créer une application Sails.js dans votre environnement de développement

Commencez par créer rapidement une application Sails.js par défaut en procédant comme suit :

1. Ouvrez le terminal de ligne de commande de votre choix et tapez la commande `CD` pointant vers un répertoire de travail.

2. Créez une application Sails.js et exécutez-la :

        sails new <appname>
        cd <appname>
        sails lift

    Vérifiez que vous pouvez accéder à la page d’accueil par défaut à l’adresse http://localhost:1377.

## Étape 2 : Créer la ressource d’application App Service dans Azure

Créez ensuite la ressource d’application App Service. Vous y déploierez votre application Sails.js plus tard.

1. Dans ce même terminal, connectez-vous à Azure comme suit :

        azure login

    Suivez les instructions de l’invite pour poursuivre la connexion à un compte Microsoft associé à votre abonnement Azure.

2. Vérifiez que vous êtes toujours dans le répertoire racine de votre projet Sails.js. Créez la ressource d’application d’App Service dans Azure avec un nom d’application unique à l’aide de la commande suivante. L’URL de votre application web est http://&lt;appname>.azurewebsites.net.

        azure site create --git <appname>

    Suivez les instructions pour sélectionner une région Azure vers laquelle effectuer le déploiement. Si vous n’avez jamais configuré d’informations d’identification de déploiement Git/FTP pour votre abonnement Azure, vous êtes invité à le faire.

    Une fois que la ressource d’application App Service est créée :

    - L’application Sails.js est initialisée dans Git.
    - Votre dépôt local initialisé dans Git est connecté à la nouvelle application App Service en tant que Git distant, intelligemment nommé « azure ».
    - Un fichier iisnode.yml est créé dans votre répertoire racine. Ce fichier permet de configurer [iisnode](https://github.com/tjanczuk/iisnode), qu’App Service utilise pour exécuter des applications Node.js.

## Étape 3 : Configurer et déployer votre application Sails.js

 L’utilisation d’une application Sails.js dans App Service se compose de trois étapes principales :

 - Configurez votre application pour qu’elle s’exécute dans App Service.
 - Déployez-la dans App Service.
 - Lisez les journaux stderr et stdout pour résoudre les éventuels problèmes de déploiement.

Procédez comme suit :

1. Ouvrez le nouveau fichier iisnode.yml dans votre répertoire racine et ajoutez les deux lignes suivantes :

        loggingEnabled: true
        logDirectory: iisnode

    La journalisation est maintenant activée pour iisnode. Pour savoir comment cela fonctionne, consultez [Obtenir des journaux stdout et stderr à partir d’iisnode](app-service-web-nodejs-sails.md#iisnodelog).

2. Ouvrez config/env/production.js pour configurer votre environnement de production et définir `port` et `hookTimeout` :

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    La description de ces paramètres de configuration est disponible dans la [documentation Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

    Vous devez ensuite vérifier que [Grunt](https://www.npmjs.com/package/grunt) est compatible avec les lecteurs réseau Azure. Au moment de la rédaction de cet article, Grunt peut afficher l’erreur [« ENOTSUP : Opération non prise en charge sur le socket »](https://github.com/isaacs/node-glob/issues/205), car il utilise actuellement un package [glob](https://www.npmjs.com/package/glob) (v3.1.21) obsolète qui ne prend pas en charge les lecteurs réseau. Les étapes suivantes vous montrent comment faire pour que Grunt utilise [glob v5.0.14 ou une version ultérieure](https://github.com/isaacs/node-glob/commit/bf3381e90e283624fbd652835e1aefa55d45e2c7).

3. Étant donné que `npm install` a déjà été exécuté lors de la création de votre application, générez npm-shrinkwrap.json à la racine du projet :

        npm shrinkwrap

4. Ouvrez npm-shrinkwrap.json, recherchez le code json pour `"grunt":`, puis ajoutez la dépendance de la version glob souhaitée. Le code json finalisé doit avoir l’aspect suivant :

        "grunt": {
            "version": "0.4.5",
            "from": "grunt@0.4.5",
            "resolved": "https://registry.npmjs.org/grunt/-/grunt-0.4.5.tgz",
            "dependencies": {
                "glob": {
                    "version": "5.0.14",
                    "from": "glob@5.0.14",
                    "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
                }
            }
        },

5. Recherchez toutes les références à glob en recherchant `"glob":`. Si une des références est v3.1.21 ou inférieure, modifiez le code json comme suit :

        "glob": {
            "version": "5.0.14",
            "from": "glob@5.0.14",
            "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
        }

6. Enregistrez vos modifications et testez-les pour vérifier que votre application continue de s’exécuter localement :

        npm install
        sails lift

4. Utilisez maintenant Git pour déployer votre application dans Azure :

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Pour terminer, lancez votre application Azure en direct dans le navigateur :

        azure site browse

    Vous devez maintenant voir la même page d’accueil Sails.js que ci-dessous.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## Résoudre les problèmes de votre déploiement

Si votre application Sails.js échoue pour une raison quelconque dans App Service, référez-vous aux journaux stderr pour résoudre le problème. Pour plus d’informations, consultez [Obtenir des journaux stdout et stderr à partir d’iisnode](app-service-web-nodejs-sails.md#iisnodelog). Si l’application a correctement démarré, le journal stdout doit afficher le message familier suivant :

                .-..-.

    Sails              <|    .-..-.
    v0.12.1             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\a76e8111-663e-449d-956e-5c5deff2d304
    To shut down Sails, press <CTRL> + C at any time.

## Se connecter à une base de données dans Azure

Pour vous connecter à une base de données Azure, créez la base de données de votre choix, telle que Azure SQL Database, MySQL, MongoDB, Cache (Redis) Azure, etc. et utilisez l’[adaptateur de banque de données](https://github.com/balderdashy/sails#compatibility) correspondant pour la connexion. Les étapes de cette section montrent comment se connecter à une base de données SQL Azure.

1. Suivez [ce didacticiel](../sql-database/sql-database-get-started.md) pour créer une base de données SQL Azure vide dans un nouveau serveur SQL Server. Les paramètres de pare-feu par défaut autorisent les services Azure (par exemple App Service) à s’y connecter.

2. Dans votre terminal de ligne de commande, installez l’adaptateur SQL Server :

        npm install sails-sqlserver --save

    Étant donné que vous avez modifié package.json, vous devez régénérer npm-shrinkwrap.json. Vous le ferez par la suite.
    
3. Supprimez le répertoire node\_modules/.

4. Exécutez `npm shrinkwrap`.

5. Rouvrez npm-shrinkwrap.json et mettez à jour les versions de package `glob` comme vous l’avez fait dans la section précédente.

    Revenons maintenant à la tâche principale.
        
3. Ouvrez config/connections.js et ajoutez le code json suivant à la liste des adaptateurs :

        sqlserver: {
            adapter: 'sails-sqlserver',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.sqlserver, 
            database: process.env.dbname,
            options: {
                encrypt: true   // use this for Azure databases
            }
        },

4. Vous devez définir chaque variable d’environnement (`process.env.*`) dans App Service. Pour ce faire, exécutez les commandes suivantes à partir de votre terminal :

        azure site appsetting add dbuser="<database server administrator>"
        azure site appsetting add dbpassword="<database server password>"
        azure site appsetting add sqlserver="<database server name>.database.windows.net"
        azure site appsetting add dbname="<database name>"
        
4. Ouvrez config/env/production.js pour configurer votre environnement de production et définissez `connection` et `migrate` dans l’objet JSON `models` :

        models: {
            connection: 'sqlserver',
            migrate: 'alter'
        },

4. Dans le terminal, [générez](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) un [modèle d’API](http://sailsjs.org/documentation/concepts/blueprints) Sails.js en suivant la procédure habituelle. Par exemple :

         sails generate api mywidget
     
5. Enregistrez l’ensemble de vos modifications, envoyez-les dans Azure et accédez à votre application pour vérifier qu’elle fonctionne toujours.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. À présent, accédez au modèle d’API que vous venez de créer dans le navigateur. Par exemple :

        http://<appname>.azurewebsites.net/widget/create
    
    L’API doit vous renvoyer l’entrée créée dans la fenêtre du navigateur :
    
        {"id":1,"createdAt":"2016-03-28T23:08:01.000Z","updatedAt":"2016-03-28T23:08:01.000Z"}

## Autres ressources

- [Prise en main des applications web Node.js dans Azure App Service](app-service-web-nodejs-get-started.md)
- [Utilisation de modules Node.js avec des applications Azure](../nodejs-use-node-modules-azure-apps.md)

<!---HONumber=AcomDC_0406_2016-->