---
title: "Créer une application web Node.js et MongoDB dans Azure | Microsoft Docs"
description: "Apprenez comment faire fonctionner une application Node.js dans Azure, avec une connexion à une base de données Cosmos DB, via une chaîne de connexion MongoDB."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 2a3d63b3829e750b62658d720522ae1abf89cd86
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Créer une application web Node.js et MongoDB dans Azure
Ce didacticiel vous montre comment créer une application web Node.js dans Azure et comment la connecter à une base de données. Lorsque vous aurez terminé, vous disposerez d’une MEAN (MongoDB, Express, AngularJS et Node.js) exécutée sous [Azure App Service Web Apps](app-service-web-overview.md). Pour plus de simplicité, l’exemple d’application utilise [l’infrastructure de développement web MEAN.js](http://meanjs.org/).

![Application MEAN.js exécutée dans Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Grâce à ce didacticiel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une base de données MongoDB dans Azure
> * Connecter une application Node.js à MongoDB
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gestion de l’application dans le portail Azure

## <a name="prerequisites"></a>Composants requis

Avant d’exécuter cet exemple, tenez compte des conditions préalables suivantes :

1. [Téléchargement et installation de GIT](https://git-scm.com/)
1. [Téléchargement et installation de Node.js et NPM](https://nodejs.org/)
1. [Installer Gulp.js](http://gulpjs.com/) (requis par [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Téléchargement, installation et exécution de MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 
1. [Téléchargement et installation de l’interface Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb"></a>Tester la base de données MongoDB locale
Dans cette étape, vous allez vérifier le bon fonctionnement de votre base de données MongoDB locale.

Ouvrez la fenêtre du terminal et `cd` dans le répertoire `bin` de votre installation MongoDB. 

Exécutez `mongo` dans le terminal pour vous connecter à votre serveur MongoDB local.

```bash
mongo
```

Si la connexion est établie, cela signifie que votre base de données MongoDB est déjà en cours d’exécution. Dans le cas contraire, assurez-vous que votre base de données MongoDB locale est démarrée en suivant les étapes de [télécharger, d’installer et d’exécution de MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). Souvent, MongoDB est installé, mais vous devez néanmoins le démarrer en exécutant `mongod`. 

Lorsque vous avez terminé de tester votre base de données MongoDB, tapez `Ctrl` + `C` dans le terminal. 

<a name="step2"></a>

## <a name="create-local-nodejs-app"></a>Créer une application Node.js locale
Cette étape consiste à configurer le projet Node.js local.

### <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Ouvrez la fenêtre de terminal et `cd` dans un répertoire de travail.  

Exécutez les commandes suivantes pour cloner l’exemple de référentiel. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Cet exemple de référentiel contient une copie du référentiel [MEAN.js](https://github.com/meanjs/mean). Il est modifié au minimum pour s’exécuter sous App Service (pour plus d’informations, consultez le fichier [Lisez-moi](https://github.com/Azure-Samples/meanjs/blob/master/README.md)).

### <a name="run-the-application"></a>Exécution de l'application

Installez les packages requis et démarrez l’application.

```bash
cd meanjs
npm install
npm start
```

Lorsque l’application est entièrement chargée, vous devriez obtenir un message similaire à celui-ci :

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Dans un navigateur, accédez à `http://localhost:3000`. Cliquez sur **S’inscrire** dans le menu supérieur et essayez de créer un utilisateur fictif. 

L’exemple d’application MEAN.js stocke les données utilisateur dans la base de données. Si vous réussissez et si MEAN.js se connecte automatiquement à l’utilisateur créé, cela signifie que votre application écrit des données dans la base de données MongoDB locale.

![MEAN.js se connecte correctement à MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Essayez de cliquer sur **Admin** > **Manage Articles** (Administrateur > Gérer les articles) pour ajouter des articles.

Pour arrêter Node.js à tout moment, tapez `Ctrl`+`C` dans le terminal. 

## <a name="create-production-mongodb"></a>Créer une base de données MongoDB de production

Dans cette étape, vous allez créer une base de données MongoDB dans Azure. Lorsque votre application est déployée sur Azure, elle utilise cette base de données pour sa charge de travail de production.

Pour MongoDB, ce didacticiel utilise [Azure Cosmos DB](/azure/documentdb/). Cosmos DB prend en charge les connexions client MongoDB.

### <a name="log-in-to-azure"></a>Connexion à Azure

Vous allez maintenant utiliser l’interface Azure CLI 2.0 dans une fenêtre de terminal pour créer les ressources nécessaires pour héberger votre application Node.js dans Azure App Service.  Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les applications web, les bases de données et les comptes de stockage sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources en Europe de l’Ouest.

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

Pour connaître les valeurs que vous pouvez utiliser pour `--location`, utilisez la commande d’interface de ligne de commande Azure `az appservice list-locations`.

### <a name="create-a-cosmos-db-account"></a>Création d’un compte Cosmos DB

Créez un compte Cosmos DB à l’aide de la commande [az cosmosdb create](/cli/azure/cosmosdb#create).

Dans la commande suivante, remplacez votre nom unique Cosmos DB là où se trouve l’espace réservé _&lt;nom_cosmosdb>_. Ce nom unique est utilisé en tant que point de terminaison Cosmos DB, `https://<cosmosdb_name>.documents.azure.com/`. Pour cette raison, le nom doit être unique sur l’ensemble des comptes Cosmos DB dans Azure. 

```azurecli
az cosmosdb create \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup \
    --kind MongoDB
```

Le paramètre `--kind MongoDB` prend en charge les connexions clientes MongoDB.

> [!NOTE]
> Le _&lt;nom_cosmosdb>_ ne peut contenir que des minuscules, des chiffres, le caractère _-_, et doit compter entre 3 et 50 caractères.
>
>

Une fois le compte Cosmos DB créé, Azure CLI affiche des informations similaires à celles de l’exemple suivant :

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output has been truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Connecter l’application à la production MongoDB

Pendant cette étape, vous connectez votre exemple d’application MEAN.js à la base de données Cosmos DB que vous venez de créer, en utilisant une chaîne de connexion MongoDB. 

### <a name="retrieve-the-database-key"></a>Récupérer la clé de la base de données

Pour se connecter à la base de données Cosmos DB, vous avez besoin de la clé de la base de données. Utilisez la commande [az documentdb list-keys](/cli/azure/cosmosdb#list-keys) pour récupérer la clé primaire.

```azurecli
az cosmosdb list-keys \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup
```

L’interface Azure CLI génère des informations semblables à ce qui suit :

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Copiez la valeur de `primaryMasterKey` dans un éditeur de texte. Vous aurez besoin de ces informations dans l’étape suivante.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurer la chaîne de connexion dans votre application Node.js

Dans votre référentiel MEAN.js, ouvrez _config/env/production.js_.

Dans l’objet `db`, remplacez la valeur de `uri` comme indiqué dans l’exemple suivant. Veillez également à remplacer les deux espaces réservés _&lt;nom_cosmosdb>_ par le nom de votre base de données Cosmos DB, et remplacez l’espace réservé _&lt;clé_primaire_principale>_ par la clé que vous avez copiée à l’étape précédente.

```javascript
db: {
  uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

> [!NOTE] 
> L’option `ssl=true` est importante, car [Cosmos DB nécessite SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 
>
>

Enregistrez vos modifications.

### <a name="test-the-application-in-production-mode"></a>Tester l’application en mode de production 

Comme certaines autres infrastructures web Node.js, MEAN.js utilise `gulp` pour réduire et regrouper des scripts pour l’environnement de production. Cette opération génère les fichiers nécessaires à l’environnement de production. 

Exécutez `gulp prod` maintenant.

```bash
gulp prod
```

Ensuite, exécutez la commande suivante pour utiliser la chaîne de connexion que vous avez configurée dans _config/env/production.js_.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` définit la variable d’environnement qui indique à Node.js de s’exécuter dans l’environnement de production, et `node server.js` démarre le serveur Node.js avec `server.js` à la racine de votre référentiel. Voici comment est chargée votre application Node.js dans Azure. 

Lorsque l’application est chargée, assurez-vous qu’elle s’exécute en production :

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Dans un navigateur, accédez à `http://localhost:8443`. Cliquez sur **S’inscrire** dans le menu supérieur et essayez de créer un utilisateur fictif, comme précédemment. Si vous y parvenez, cela signifie que votre application écrit des données dans la base de données Cosmos DB dans Azure. 

Dans le terminal, arrêtez Node.js en tapant `Ctrl`+`C`. 

## <a name="deploy-app-to-azure"></a>Déployer des applications dans Azure
Dans cette étape, vous allez déployer dans Azure App Service votre application Node.js connectée à MongoDB.

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

Créez un plan App Service avec la commande [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

L’exemple suivant crée un plan App Service nommé _myAppServicePlan_ en indiquant le niveau tarifaire **Gratuit** :

```azurecli
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

Lorsque le plan App Service est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
```

### <a name="create-a-web-app"></a>Créer une application web

Maintenant qu’un plan App Service est créé, générez une application web dans le plan App Service _myAppServicePlan_. L’application web vous offre un espace d’hébergement pour déployer votre code, et fournit une URL pour vous permettre d’afficher l’application déployée. Utilisez la commande [az appservice web create](/cli/azure/appservice/web#create) pour créer l’application web. 

Dans la commande suivante, remplacez l’espace réservé _&lt;app_name>_ par le nom unique de votre application. Ce nom unique est utilisé dans le nom de domaine par défaut de l’application web. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous pouvez ultérieurement mapper toute entrée DNS personnalisée vers l’application web avant de l’exposer à vos utilisateurs. 

```azurecli
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Une fois l’application web créée, Azure CLI affiche des informations similaires à celles de l’exemple suivant : 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-an-environment-variable"></a>Configurer une variable d’environnement

Précédemment dans ce didacticiel, vous avez codé en dur la chaîne de connexion de la base de données dans _config/env/prodution.js_. Conformément aux meilleures pratiques de sécurité, vous allez vouloir conserver ces données sensibles en dehors de votre référentiel Git. Pour l’application en cours d’exécution dans Azure, vous utiliserez plutôt une variable d’environnement.

Dans App Service, vous définissez les variables d’environnement en tant que _paramètres d’application_ à l’aide de la commande [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

L’exemple suivant vous permet de configurer un paramètre d’application `MONGODB_URI` dans votre application web Azure. Veillez à remplacer les espaces réservés comme précédemment.

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

Dans le code Node.js, vous accédez à ce paramètre d’application avec `process.env.MONGODB_URI`, comme vous accéderiez à n’importe quelle variable d’environnement. 

À présent, annulez vos modifications apportées à _config/env/production.js_ à l’aide de la commande suivante :

```bash
git checkout -- .
```

Ouvrez à nouveau _config/env/production.js_. Notez que l’application MEAN.js par défaut est déjà configurée pour utiliser la variable d’environnement `MONGODB_URI` que vous venez de créer.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>Configurer le déploiement Git local 

Vous pouvez déployer votre application dans Azure App Service de plusieurs façons, notamment FTP, Git local ainsi que GitHub, Visual Studio Team Services et Bitbucket. Pour les sites FTP et Git locaux, il est nécessaire de disposer d’un utilisateur de déploiement configuré sur le serveur pour authentifier votre déploiement. 

Utilisez la commande [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) pour créer vos informations d’identification au niveau des comptes. 

> [!NOTE] 
> Un utilisateur de déploiement est requis pour les déploiements FTP et Git en local sur App Service. Cet utilisateur de déploiement se situe au niveau des comptes. Par conséquent, il est différent de votre compte d’abonnement Azure. Vous n’avez à configurer cet utilisateur de déploiement qu’une seule fois.

```azurecli
az appservice web deployment user set \
    --user-name <specify-a-username> \
    --password <minimum-8-char-capital-lowercase-number>
```

Utilisez la commande [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) pour configurer l’accès Git local à l’application web Azure. 

```azurecli
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
```

Lorsque l’utilisateur du déploiement est configuré, l’interface Azure CLI affiche l’URL de déploiement pour votre application web Azure au format suivant :

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

Copiez la sortie du terminal, car elle sera utilisée à l’étape suivante. 

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

Ajoutez un référentiel distant Azure dans votre référentiel Git local. 

```bash
git remote add azure <paste_copied_url_here> 
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application Node.js. Le mot de passe que vous avez fourni précédemment dans le cadre de la création de l’utilisateur du déploiement vous sera demandé. 

```bash
git push azure master
```

Au cours du déploiement, Azure App Service communique sa progression avec Git.

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

> [!NOTE]
> Vous remarquerez peut-être que le processus de déploiement exécute [Gulp](http://gulpjs.com/) après `npm install`. App Service n’exécute pas les tâches Gulp ou Grunt pendant le déploiement ; cet exemple de référentiel possède donc deux fichiers supplémentaires dans son répertoire racine pour l’activer : 
>
> - _.deployment_ : ce fichier indique à App Service d’exécuter `bash deploy.sh` en tant que script de déploiement personnalisé.
> - _deploy.sh_ : le script de déploiement personnalisé. Si vous examinez le fichier, vous verrez qu’il exécute `gulp prod` après `npm install` et `bower install`. 
>
> Vous pouvez utiliser cette approche pour ajouter une étape à votre déploiement Git.
>
> Notez que si vous redémarrez votre application web Azure à tout moment, App Service ne réexécute pas ces tâches d’automatisation.
>
>

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure 
Accédez à l’application web déployée à l’aide de votre navigateur web. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Cliquez sur **S’inscrire** dans le menu supérieur et essayez de créer un utilisateur fictif. 

Si vous réussissez, et si l’application se connecte automatiquement à l’utilisateur créé, cela signifie que votre application MEAN.js dans Azure dispose d’une connectivité à la base de données MongoDB (Cosmos DB). 

![Application MEAN.js exécutée dans Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Essayez de cliquer sur **Admin** > **Manage Articles** (Administrateur > Gérer les articles) pour ajouter des articles. 

**Félicitations !** Vous exécutez une application Node.js orientée données dans Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Mettre à jour le modèle de données et redéployer

Dans cette étape, vous allez apporter certaines modifications au modèle de données `article` et publier vos modifications dans Azure.

### <a name="update-the-data-model"></a>Mettre à jour le modèle de données

Ouvrez _modules/articles/server/models/article.server.model.js_.

Dans `ArticleSchema`, ajoutez un type `String` appelé `comment`. Lorsque vous aurez terminé, votre code de schéma doit ressembler à ceci :

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

Vous avez terminé les modifications du modèle. 

### <a name="update-the-articles-code"></a>Mettre à jour le code d’articles

Ensuite, mettez à jour le reste de votre code `articles` afin d’utiliser `comment`.

En tout, vous avez cinq (5) fichiers à modifier : le contrôleur de serveur et les quatre vues clientes. 

Tout d’abord, ouvrez _modules/articles/server/controllers/articles.server.controller.js_.

Dans la fonction `update`, ajoutez une affectation de `article.comment`. Lorsque vous avez terminé, votre fonction `update` doit ressembler à ceci :

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Ensuite, ouvrez _modules/articles/client/views/view-article.client.view.html_.

Juste au-dessus de la balise de fermeture `</section>`, ajoutez la ligne suivante pour afficher `comment` avec le reste des données de l’article :

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Ensuite, ouvrez _modules/articles/client/views/list-articles.client.view.html_.

Juste au-dessus de la balise de fermeture `</a>`, ajoutez la ligne suivante pour afficher `comment` avec le reste des données de l’article :

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Ensuite, ouvrez _modules/articles/client/views/admin/list-articles.client.view.html_.

À l’intérieur de la balise `<div class="list-group">`, juste au-dessus de la balise de fermeture `</a>`, ajoutez la ligne suivante pour afficher `comment` avec le reste des données de l’article :

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Pour finir, ouvrez _modules/articles/client/views/admin/form-article.client.view.html_.

Recherchez la balise `<div class="form-group">` qui contient le bouton Envoyer, qui ressemble à ceci :

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Juste au-dessus de cette balise, ajoutez une autre balise `<div class="form-group">` permettant aux utilisateurs de modifier le champ `comment`. Votre nouvelle balise doit avoir l’aspect suivant :

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Tester vos modifications en local

Enregistrez toutes vos modifications.

Testez de nouveau vos modifications en mode de production.

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> N’oubliez pas que votre _config/env/production.js_ a été rétabli et que la variable d’environnement `MONGODB_URI` est définie uniquement dans votre application web Azure, et non sur votre ordinateur local. Si vous examinez le fichier de configuration, vous verrez que la configuration de production utilise par défaut une base de données MongoDB. Cela vous évite de toucher aux données de production lorsque vous testez vos changements de code en local.
>
>

Accédez à `http://localhost:8443` dans un navigateur et assurez-vous que vous êtes connecté.

Cliquez sur **Admin** > **Manage Articles** (Administrateur > Gérer les articles), puis ajoutez un article en cliquant sur le bouton **+**.

Vous devriez maintenant voir la nouvelle zone de texte `Comment`.

![Champ de commentaire ajouté aux Articles](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

Dans le terminal, arrêtez Node.js en tapant `Ctrl`+`C`. 

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Validez vos modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Une fois le `git push` terminé, accédez à votre application web Azure et essayez de nouveau la nouvelle fonctionnalité.

![Modifications du modèle et de la base de données publiées dans Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

> [!NOTE]
> Si vous avez ajouté des articles précédemment, vous pouvez toujours les visualiser. Les données existantes dans votre Cosmos DB ne sont pas perdues. De plus, les mises à jour que vous appliquez au schéma de données n’affectent pas vos données existantes.
>
>

## <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic 

Pendant l’exécution de votre application Node.js dans Azure App Service, vous pouvez acheminer les journaux de la console directement vers votre terminal. De cette façon, vous pouvez obtenir les mêmes messages de diagnostic pour vous aider à déboguer les erreurs d’application.

Pour démarrer la diffusion en continu de journaux, utilisez la commande [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

Une fois que la diffusion a démarré, actualisez votre application web Azure dans le navigateur pour générer un trafic web. Vous devriez maintenant voir les journaux de la console dirigés vers votre terminal.

Pour arrêter la diffusion de journaux à tout moment, tapez `Ctrl`+`C`. 

## <a name="manage-your-azure-web-app"></a>Gérer votre application web Azure

Accédez au portail Azure pour voir l’application web que vous avez créée.

Pour ce faire, connectez-vous au portail : [https://portal.azure.com](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **App Service**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Vous accédez au _panneau_ de votre application web (une page du portail qui s’ouvre horizontalement).

Par défaut, le panneau de votre application web affiche la page **Présentation**. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets figurant sur le côté gauche du panneau affichent les différentes pages de configuration que vous pouvez ouvrir.

![Panneau App Service sur le portail Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

Ces onglets affichent les nombreuses fonctionnalités exceptionnelles que vous pouvez ajouter à votre application web. La liste suivante fournit quelques exemples des possibilités :

* Mapper un nom DNS personnalisé
* Lier un certificat SSL personnalisé
* Configurer le déploiement continu
* Montée en puissance et augmentation de la taille des instances
* Ajouter une authentification utilisateur

## <a name="clean-up-resources"></a>Suppression des ressources
 
Si vous n’avez pas besoin de ces ressources pour un autre didacticiel (voir [Étapes suivantes](#next)), vous pouvez les supprimer en exécutant la commande suivante : 
  
```azurecli 
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une base de données MongoDB dans Azure
> * Connecter une application Node.js à MongoDB
> * Déploiement de l’application dans Azure
> * Mettre à jour le modèle de données et redéployer l’application
> * Diffuser des journaux à partir d’Azure vers votre terminal
> * Gérer l’application dans le portail Azure

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à une application web.

> [!div class="nextstepaction"] 
> [Mapper un nom DNS personnalisé existant à des applications web Azure](app-service-web-tutorial-custom-domain.md)

