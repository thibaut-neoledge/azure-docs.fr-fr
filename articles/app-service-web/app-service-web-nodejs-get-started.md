---
title: "Bien démarrer avec les applications web Node.js dans Azure App Service | Microsoft Docs"
description: "Découvrez comment déployer une application Node.js sur une application web dans Azure App Service."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: fb2b90c8-02b6-4700-929b-5de9a35d67cc
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 88405a9e67eb748acc9564022283004b5ebfcf48
ms.openlocfilehash: 63210a5539d1e5e5b7d1f5a60048d507e53038a5


---
# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Prise en main des applications web Node.js dans Azure App Service
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Ce didacticiel explique comment créer une application [Node.js] simple et la déployer dans [Azure App Service] à partir d’un environnement de ligne de commande comme cmd.exe ou bash. Les instructions de ce didacticiel s’appliquent à tous les systèmes d’exploitation pouvant exécuter Node.js.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

<a name="prereq"></a>

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](app-service-web-nodejs-get-started-cli-nodejs.md) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager
- [Azure CLI 2.0 (version préliminaire)](app-service-web-nodejs-get-started.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="prerequisites"></a>Composants requis
* [Node.js]
* [Bower]
* [Yeoman]
* [Git]
* [Version préliminaire d’Azure CLI 2.0](/cli/azure/install-az-cli2)
* Un compte Microsoft Azure Si vous n’avez pas de compte, vous pouvez [vous inscrire pour un essai gratuit] ou [activer les avantages de votre abonnement Visual Studio].

> [!NOTE]
> Vous pouvez [essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751) sans compte Azure. Créez une application de base et expérimentez-la pendant une heure, sans carte de paiement et sans engagement.
> 
> 

## <a name="create-and-configure-a-simple-nodejs-app-for-azure"></a>Création et configuration d’une simple application Node.js pour Azure
1. Ouvrez le terminal de ligne de commande de votre choix et installez le [générateur Express pour Yeoman].
   
        npm install -g generator-express

2. `CD` dans un répertoire de travail et générez une application expresse en utilisant la syntaxe suivante :
   
        yo express
   
    Choisissez les options suivantes lorsque vous y êtes invité :  
   
    `? Would you like to create a new directory for your project?` **Yes**  
    `? Enter directory name` **{appname}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **None**  
    `? Select a database to use:` **None**  
    `? Select a build tool to use:` **Grunt**

3. Exécutez la commande `CD` dans le répertoire racine de votre nouvelle application et démarrez-la pour vérifier qu’elle s’exécute correctement dans votre environnement de développement :
   
        npm start
   
    Dans votre navigateur, accédez à <http://localhost:3000> pour vérifier que la page d’accueil Express s’affiche. Une fois que vous êtes sûr que votre application s’exécute correctement, utilisez la commande `Ctrl-C` pour l’arrêter.

6. Ouvrez le fichier ./config/config.js à partir de la racine de votre application, et définissez le port de production sur `process.env.port` ; votre propriété `production` de l’objet `config` doit ressembler à l’exemple suivant :
   
        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }
   
    > [!NOTE] 
    > Par défaut, Azure App Service exécute les applications Node.js avec les variables d’environnement `production` (`process.env.NODE_ENV="production"`).
    > Votre configuration permet donc à votre application Node.js dans Azure de répondre aux requêtes web sur le port par défaut écouté par iisnode.
    >
    >

7. Ouvrez ./package.json et ajoutez la propriété `engines` pour [spécifier la version de Node.js](#version).
   
        "engines": {
            "node": "6.9.1"
        }, 

8. Enregistrez vos modifications, puis initialisez un référentiel Git à la racine de votre application et validez votre code :
   
        git add .
        git add -f config
        git commit -m "{your commit message}"

## <a name="deploy-your-nodejs-app-to-azure"></a>Déploiement de votre application Node.js dans Azure

1. Connectez-vous à Azure (vous avez besoin [de la version préliminaire d’Azure CLI 2.0](#prereq)) :
   
        az login
   
    Suivez les instructions de l’invite pour poursuivre la connexion à un compte Microsoft associé à votre abonnement Azure.

3. Définissez l’utilisateur de déploiement pour App Service. Vous déploierez le code ultérieurement à l’aide de ces informations d’identification.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md). Pour ce didacticiel node.js, il n’est pas vraiment nécessaire de vous familiariser avec l’outil.

        az group create --location "<location>" --name my-nodejs-app-group

    Pour connaître les valeurs possibles que vous pouvez utiliser pour `<location>`, utilisez la commande CLI `az appservice list-locations`.

3. Créez un [plan App Service plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) appelé « FREE ». Pour ce didacticiel node.js, sachez que vous ne serez pas facturé pour les applications web utilisées dans ce plan.

        az appservice plan create --name my-nodejs-appservice-plan --resource-group my-nodejs-app-group --sku FREE

4. Créez une application web avec un nom unique dans `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-nodejs-app-group --plan my-nodejs-appservice-plan

5. Configurez le déploiement Git local pour votre nouvelle application web avec la commande suivante :

        az appservice web source-control config-local-git --name <app_name> --resource-group my-nodejs-app-group

    Vous obtenez une sortie JSON similaire à ce qui suit, ce qui signifie que le référentiel Git distant est configuré :

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Ajoutez l’URL dans le fichier JSON en tant que Git distant pour votre référentiel local (appelé `azure` par souci de simplicité).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Déployez votre exemple de code dans le Git distant `azure`. Lorsque vous y êtes invité, utilisez les informations d’identification de déploiement que vous avez configurées précédemment.

        git push azure master
   
    Le générateur Express fournit un fichier .gitignore, de sorte que votre commande `git push` ne consomme pas de bande passante en tentant de charger le répertoire node_modules/.

9. Pour terminer, lancez votre application Azure en direct dans le navigateur :
   
        az appservice web browse --name <app_name> --resource-group my-nodejs-app-group
   
    Vous devez maintenant voir votre application web Node.js s’exécuter dans Azure App Service.
   
    ![Exemple d’accès à l’application déployée.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Mise à jour de votre application web Node.js
Pour mettre à jour votre application web Node.js dans App Service, il vous suffit d’exécuter `git add`, `git commit` et `git push` comme vous l’avez fait lors du premier déploiement de votre application web.

## <a name="how-app-service-deploys-your-nodejs-app"></a>Comment le App Service déploie votre application Node.js
Azure App Service utilise [iisnode] pour exécuter les applications Node.js. Azure CLI 2.0 (version préliminaire) et le moteur Kudu (déploiement Git) fonctionnent en parallèle afin de simplifier votre travail de développement et de déploiement d’applications Node.js à partir de la ligne de commande. 

* Vous pouvez créer un fichier iisnode.yml dans votre répertoire racine et l’utiliser pour personnaliser les propriétés d’iisnode. Tous les paramètres configurables sont documentées [ici](https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/iisnode.yml).
* Sur la ligne `git push azure master`, Kudu automatise les tâches de déploiement suivantes :
  
  * Si le fichier package.json se trouve à la racine du référentiel, exécutez la commande `npm install --production`.
  * Générez un fichier Web.config pour iisnode, qui pointe vers votre script de démarrage dans le fichier package.json (par exemple, server.js ou app.js).
  * Personnalisez le fichier Web.config pour préparer votre application au débogage avec Node Inspector.

## <a name="use-a-nodejs-framework"></a>Utilisation d’une infrastructure Node.js
Si vous utilisez une infrastructure Node.js répandue, telle que [Sails.js][SAILSJS] ou [MEAN.js][MEANJS] pour développer des applications, vous pouvez les déployer dans App Service. Les infrastructures Node.js les plus populaires ont des particularités bien spécifiques et leurs dépendances de package sont constamment mises à jour. Avec App Service, cependant, les journaux stdout et stderr sont mis à votre disposition afin que vous sachiez exactement ce qui se passe au niveau de votre application et que vous puissiez apporter les modifications nécessaires. Pour plus d’informations, consultez [Obtenir des journaux stdout et stderr à partir d’iisnode](#iisnodelog).

Consultez les didacticiels suivant pour savoir comment utiliser une infrastructure spécifique dans App Service :

* [Deploy a Sails.js web app to Azure App Service (Déployer une application web Sails.js dans Azure App Service)]
* [Créer une application de conversation instantanée Node.js avec Socket.IO dans Azure App Service]
* [Utilisation de io.js avec Azure App Service Web Apps]

<a name="version"></a>

## <a name="use-a-specific-nodejs-engine"></a>Utilisation d’un moteur Node.js spécifique
Dans votre flux de travail classique, vous demandez à App Service d’utiliser un moteur Node.js spécifique de la même manière que dans le fichier package.json.
Par exemple :

    "engines": {
        "node": "6.9.1"
    }, 

Le moteur de déploiement Kudu détermine le moteur Node.js à utiliser dans l’ordre suivant :

* Examinons tout d’abord iisnode.yml pour voir si `nodeProcessCommandLine` est spécifié. Si tel est le cas, vous pouvez l’utiliser.
* Ensuite, examinons le fichier package.json pour voir si `"node": "..."` est spécifié dans l’objet `engines`. Si tel est le cas, vous pouvez l’utiliser.
* Par défaut, choisissez une version de Node.js par défaut.

Pour obtenir la liste mise à jour de toutes les versions Node.js/NPM prises en charge dans Azure App Service, accédez à l’URL suivante pour votre application :

    https://<app_name>.scm.azurewebsites.net/api/diagnostics/runtime

> [!NOTE]
> Il est recommandé de définir explicitement le moteur Node.js souhaité. La version de Node.js par défaut peut changer, et vous pouvez obtenir des erreurs dans votre application web Azure, car la version de Node.js par défaut ne convient pas à votre application.
> 
> 

<a name="iisnodelog"></a>

## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Obtenir des journaux stdout et stderr à partir d’iisnode
Pour lire les journaux iisnode, suivez les étapes ci-après.

> [!NOTE]
> Après ces étapes, il se peut que les fichiers journaux ne soient générés que lorsqu’une erreur se produit.
> 
> 

1. Ouvrez le fichier iisnode.yml disponible avec Azure CLI 2.0 (version préliminaire).
2. Spécifiez les deux paramètres suivants : 
   
        loggingEnabled: true
        logDirectory: iisnode
   
    Ensemble, ces paramètres demandent à iisnode dans App Service de placer ses sorties stdout et stderror dans le répertoire D:\home\site\wwwroot\**iisnode*.
3. Enregistrez vos modifications, puis répercutez-les dans Azure à l’aide des commandes Git suivantes :
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode est désormais configuré. Les étapes suivantes vous montrent comment accéder à ces journaux.
4. Dans votre navigateur, accédez à la console de débogage Kudu correspondant à votre application, c’est-à-dire :
   
        https://{appname}.scm.azurewebsites.net/DebugConsole 
   
    Cette URL diffère de celle de l’application web par l’ajout de « *.scm.* » au nom DNS. Si vous omettez cet ajout à l’URL, vous obtiendrez une erreur 404.
5. Accédez à D:\home\site\wwwroot\iisnode
   
    ![Accès à l’emplacement des fichiers journaux iisnode.][iislog-kudu-console-find]
6. Cliquez sur l’icône **Modifier** du journal que vous souhaitez lire. Vous pouvez également cliquer sur **Télécharger** ou **Supprimer** si vous le souhaitez.
   
    ![Ouverture d’un fichier journal iisnode.][iislog-kudu-console-open]
   
    Vous pouvez maintenant consulter le journal pour vous aider à résoudre les problèmes liés à votre déploiement d’App Service.
   
    ![Examen d’un fichier journal iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Débogage de votre application avec Node-Inspector
Si vous utilisez Node-Inspector pour déboguer vos applications Node.js, vous pouvez l’utiliser pour votre application App Service active. Node-Inspector est préinstallé avec l’installation d’iisnode pour App Service. Si vous utilisez Git pour le déploiement, le fichier Web.config généré automatiquement à partir de Kudu contient déjà toute la configuration dont vous avez besoin pour activer Node-Inspector.

Pour activer Node-Inspector, procédez comme suit :

1. Ouvrez iisnode.yml à la racine de votre référentiel et spécifiez les paramètres suivants : 
   
        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll
2. Enregistrez vos modifications, puis répercutez-les dans Azure à l’aide des commandes Git suivantes :
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
3. Il vous suffit maintenant d’accéder au fichier de démarrage de votre application, comme spécifié par le script de démarrage contenu dans votre fichier package.json en ajoutant /debug à l’URL. Par exemple,
   
        http://{appname}.azurewebsites.net/server.js/debug
   
    Ou,
   
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Autres ressources
* [Spécification d'une version Node.js dans une application Azure](../nodejs-specify-node-version-azure-apps.md)
* [Meilleures pratiques et guide de dépannage pour les applications Node.js sur Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
* [Débogage d’une application web Node.js dans Azure Web Service](web-sites-nodejs-debug.md)
* [Utilisation de modules Node.js avec des applications Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps : Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
* [Centre de développement Node.js](/develop/nodejs/)
* [Prise en main des applications web dans Azure App Service.](app-service-web-get-started.md)
* [Exploring the Super Secret Kudu Debug Console (Exploration de la console de débogage Kudu Super Secret)]

<!-- URL List -->

[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[activer les avantages de votre abonnement Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Créer une application de conversation instantanée Node.js avec Socket.IO dans Azure App Service]: ./web-sites-nodejs-chat-app-socketio.md
[Deploy a Sails.js web app to Azure App Service (Déployer une application web Sails.js dans Azure App Service)]: ./app-service-web-nodejs-sails.md
[Exploring the Super Secret Kudu Debug Console (Exploration de la console de débogage Kudu Super Secret)]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[générateur Express pour Yeoman]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Utilisation de io.js avec Azure App Service Web Apps]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[vous inscrire pour un essai gratuit]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png



<!--HONumber=Jan17_HO3-->


