---
title: "Créer une application web Docker Python et PostgreSQL dans Azure | Microsoft Docs"
description: "Découvrez comment faire fonctionner une application Docker Python dans Azure en établissant une connexion à une base de données PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
editor: 
ms.assetid: 2bada123-ef18-44e5-be71-e16323b20466
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/03/2017
ms.author: beverst
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: ca086f76e50cb27f012bb2e7f05595be5fdcb241
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Créer une application web Docker Python et PostgreSQL dans Azure
Ce didacticiel vous montre comment créer une application web de base Docker Python dans Azure. Vous allez également connecter cette application à une base de données PostgreSQL. Lorsque vous avez terminé, vous disposez d’une application Python Flask s’exécutant dans un conteneur Docker sur les [App Service Web Apps](app-service-web-overview.md).

![Application Docker Python Flask dans Azure App Service](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="before-you-begin"></a>Avant de commencer

Avant d’exécuter cet exemple, tenez compte des conditions préalables suivantes :

1. [Téléchargement et installation de GIT](https://git-scm.com/)
1. [Téléchargement et installation de Python](https://www.python.org/downloads/)
1. [Téléchargement, installation et exécution de PostgreSQL](https://www.postgresql.org/download/)
1. [Télécharger et installation de Docker Community Edition](https://www.docker.com/community-edition)
1. [Téléchargement et installation de l’interface Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Test de l’installation PostgreSQL locale et création d’une base de données
Dans cette étape, vous allez vérifier le bon fonctionnement de votre base de données PostgreSQL locale.

Ouvrez la fenêtre de terminal et exécutez `psql postgres` pour vous connecter à votre serveur PostgreSQL local.

```bash
psql postgres
```

Si la connexion est établie, cela signifie que votre base de données PostgreSQL est déjà en cours d’exécution. Dans le cas contraire, assurez-vous que votre base de données MongoDB locale est démarrée en suivant les étapes à la page [Téléchargement, installation et exécution de PostgreSQL](https://www.postgresql.org/download/).

Créez une base de données appelée `eventregistration` et configurez un utilisateur de base de données distinct nommé `manager` avec le mot de passe `supersecretpass`.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Saisissez `\q` pour quitter le client PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Création d’une application Python Flask locale
Cette étape consiste à configurer le projet Python Flask local.

### <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Ouvrez la fenêtre de terminal et `CD` dans un répertoire de travail.  

Exécutez les commandes suivantes pour cloner l’exemple de référentiel, et accédez à la version `0.1-initialapp`.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.gi
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Cet exemple de référentiel contient une application [Flask](http://http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Exécution de l'application

> [!NOTE] 
> Dans une étape ultérieure, nous simplifierons ce processus en créant un conteneur Docker à utiliser avec notre base de données de production.

Installez les packages requis et démarrez l’application.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Lorsque l’application est entièrement chargée, vous devriez obtenir un message similaire à celui-ci :

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Dans un navigateur, accédez à `http://127.0.0.1:5000`. Cliquez sur **S’inscrire** et essayez de créer un enregistrement factice.

![Application Python Flask s’exécutant localement](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

L’exemple d’application Flask stocke les données utilisateur dans la base de données. Si vous réussissez et êtes en mesure d’afficher votre inscription dans l’application, votre application écrit alors les données dans la base de données PostgreSQL locale.

Pour arrêter le serveur Flask à tout moment, tapez `Ctrl`+`C` dans le terminal. 

## <a name="create-a-production-postgresql-database"></a>Création d’une base de données de production PostgreSQL

Dans cette étape, vous allez créer une base de données PostgreSQL dans Azure. Lorsque votre application est déployée sur Azure, nous spécifions cette base de données pour sa charge de travail de production.

### <a name="log-in-to-azure"></a>Connexion à Azure

Vous allez maintenant utiliser l’interface de ligne de commande Azure 2.0 dans une fenêtre de terminal pour créer les ressources nécessaires pour héberger votre application Python dans Azure App Service.  Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran. 

```azurecli-interactive 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les applications web, les bases de données et les comptes de stockage sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé dans la région États-Unis de l’Ouest :

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

Pour connaître les valeurs que vous pouvez utiliser pour `--location`, utilisez la commande d’interface de ligne de commande Azure `az appservice list-locations`.

### <a name="create-an-azure-database-for-postgresql-server"></a>Création d’un serveur Azure Database pour PostgreSQL

Créez un serveur PostgreSQL avec la commande [az postgres server create](/cli/azure/documentdb#create).

Dans la commande suivante, indiquez le nom unique de votre propre serveur PostgreSQL là où se trouve l’espace réservé `<postgresql_name>`. Ce nom unique est utilisé en tant que point de terminaison PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`). Pour cette raison, le nom doit être unique sur l’ensemble des serveurs dans Azure. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <my_admin_username>
```

`--admin-user` est requis pour créer le compte d’utilisateur administrateur de la base de données initiale. Vous êtes invité à choisir un mot de passe pour cet utilisateur.

Lorsque le serveur de base de données Azure pour PostgreSQL est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="creating-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Création d’une règle de pare-feu pour le serveur de base de données Azure pour PostgreSQL

Avant de pouvoir accéder à la base de données, nous devons autoriser son accès à partir de toutes les adresses IP. Vous pouvez le faire via la commande d’interface de ligne de commande Azure suivante :

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

Lorsque le pare-feu est créé, l’interface de ligne de commande Azure vérifie la présence de règles comme suit :

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>Connexion de votre application Python Flask à la base de données

Dans cette étape, vous connectez votre exemple d’application Python Flask au serveur de base de données Azure pour PostgreSQL que vous avez créé.

### <a name="creating-an-empty-database-and-setting-up-a-new-database-application-user"></a>Création d’une base de données vide et configuration d’un nouvel utilisateur de l’application de base de données

Nous créons un utilisateur de base de données avec accès à une base de données unique seulement. Cette étape évite de donner à notre application un accès total au serveur via nos informations d’identification d’administrateur.

Connectez-vous à la base de données (vous êtes invité à indiquer votre mot de passe administrateur).
```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Ensuite, créez la base de données et l’utilisateur à partir de l’interface de ligne de commande PostgreSQL.
```
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Saisissez `\q` pour quitter le client PostgreSQL.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Test de l’application en local sur la base de données Azure PostgreSQL 

Revenons maintenant au dossier `app` du référentiel Github cloné. Nous pouvons exécuter notre application Python Flask simplement en mettant à jour nos variables d’environnement de base de données.

```bash
FLASK_APP=app.py;DBHOST="<postgresql_name>.postgres.database.azure.com";DBUSER="manager@<postgresql_name>";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Lorsque l’application est entièrement chargée, vous devriez à nouveau obtenir un message similaire à celui-ci :

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Dans un navigateur, accédez à `http://127.0.0.1:5000`. Cliquez sur **S’inscrire** et essayez de créer un enregistrement factice. Vous écrivez maintenant des données sur la base de données de production dans Azure.

![Application Python Flask s’exécutant localement](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Exécution de l’application à partir d’un conteneur Docker

Nous allons maintenant générer l’image conteneur Docker, et exécuter localement l’application à partir d’un conteneur Docker lors de la connexion à la base de données de production PostgreSQL dans Azure.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker affiche une confirmation de création du conteneur.

```
Successfully built 7548f983a36b
```

Nous allons ajouter des variables d’environnement de base de données à un fichier de variables d’environnement `db.env`.

```
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

Maintenant, nous exécutons l’application à partir du conteneur Docker. Nous spécifions le fichier de variables d’environnement, et mappons le port Flask par défaut 5000 à notre port local 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

Sans surprise, la sortie est similaire à la précédente. Toutefois, la migration de la base de données initiale n’a plus besoin d’être effectuée et elle est donc ignorée.
```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 ```

 La base de données contient déjà l’enregistrement que vous avez créé précédemment.

 ![Application Python Flask basée sur un conteneur Docker s’exécutant localement](./media/app-service-web-tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Charger le conteneur Docker vers un registre de conteneurs
Dans cette étape, vous allez charger le conteneur Docker que nous avons créé dans un registre de conteneurs. Nous allons utiliser Azure Container Registry. Toutefois, vous pouvez également utiliser les autres registres populaires tels que Docker Hub.

### <a name="create-an-azure-container-registry"></a>Création d’un Azure Container Registry

Dans la commande suivante, pour créer un registre de conteneurs, remplacez `<registry_name>` par un nom de registre de conteneurs Azure unique de votre choix.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Sortie
```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Récupération des informations d’identification du Registre pour l’extraction et la transmission d’images Docker

Nous devons activer le mode Administrateur avant de pouvoir accéder aux informations d’identification.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Vous verrez deux mots de passe. À l’avenir, notez le nom d’utilisateur et le premier mot de passe.
```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Chargement de votre conteneur Docker vers Azure Container Registry

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Déploiement de l’application Docker Python Flask sur Azure
Dans cette étape, vous déployez votre application Python Flask basée sur le conteneur Docker dans Azure App Service.

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

Créez un plan App Service avec la commande [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

L’exemple suivant crée un plan App Service Linux nommé `myAppServicePlan` en appliquant le niveau tarifaire S1 :

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Lorsque le plan App Service est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json 
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
``` 

### <a name="create-a-web-app"></a>Créer une application web

Maintenant qu’un plan App Service est créé, générez une application web dans le plan App Service `myAppServicePlan`. L’application web vous offre un espace d’hébergement pour déployer votre code, et fournit une URL pour vous permettre d’afficher l’application déployée. Utilisez la commande [az appservice web create](/cli/azure/appservice/web#create) pour créer l’application web. 

Dans la commande suivante, remplacez l’espace réservé `<app_name>` par le nom unique de votre propre application. Ce nom unique sera utilisé dans le nom de domaine par défaut de l’application web. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous pouvez ultérieurement mapper toute entrée DNS personnalisée vers l’application web avant de l’exposer à vos utilisateurs. 

```azurecli-interactive
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Une fois l’application web créée, Azure CLI affiche des informations similaires à celles de l’exemple suivant : 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-the-database-environment-variables"></a>Configuration des variables d’environnement de base de données

Précédemment dans ce didacticiel, vous avez défini manuellement des variables d’environnement pour vous connecter à notre base de données PostgreSQL.

Dans App Service, vous définissez les variables d’environnement en tant que _paramètres d’application_ à l’aide de la commande [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

Les éléments suivants vous permettent de spécifier les informations de connexion à la base de données en tant que paramètres de l’application. En outre, nous utilisons la variable `PORT` pour spécifier que vous souhaitez mapper le PORT 5000 à partir de notre conteneur Docker pour recevoir le trafic HTTP sur le PORT 80.

```azurecli-interactive
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Configuation du déploiement du conteneur Docker 

AppService peut automatiquement télécharger et exécuter un conteneur Docker.

Utilisez la commande [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) pour créer vos informations d’identification au niveau des comptes. 

```azurecli
az appservice web config container update --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Chaque fois que nous mettons à jour le conteneur Docker ou que nous modifions les paramètres ci-dessus, redémarrez l’application pour vérifier que tous les paramètres sont appliqués et que le dernier conteneur est extrait à partir du Registre.

```azurecli-interactive
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure 
Accédez à l’application web déployée à l’aide de votre navigateur web. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> La première fois que vous accédez à votre application web après une modification à la configuration du conteneur, prévoyez du temps supplémentaire pour le téléchargement et le démarrage du conteneur.

Vous verrez des invités précédemment enregistrés dans la base de données de production Azure à l’étape précédente.

 ![Application Python Flask basée sur un conteneur Docker s’exécutant localement](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Félicitations !** Vous exécutez une application Python Flask basée sur un conteneur Docker dans Azure App Service.


## <a name="update-data-model-and-redeploy"></a>Mettre à jour le modèle de données et redéployer

Dans cette étape, nous ajoutons le nombre de participants à chaque enregistrement d’événement en mettant à jour le modèle Invité.

Découvrez la version `0.2-migration` avec la commande git suivante.
```bash
git checkout tags/0.2-migration
```

Cette version a déjà apporté les modifications nécessaires à nos vues, à nos contrôleurs et à notre modèle. Elle inclut également une migration de base de données générée via *alembic* (`flask db migrate`). Vous pouvez voir toutes les modifications apportées via la commande git suivante.

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Tester vos modifications en local

Exécutez les commandes suivantes pour tester vos modifications en local en exécutant le serveur Flask.

Mac / Linux :
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Accédez à `http://127.0.0.1:5000` dans votre navigateur pour afficher les modifications. Créez un enregistrement factice.

![Application Python Flask basée sur un conteneur Docker s’exécutant localement](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Générez la nouvelle image Docker, placez-la dans le registre de conteneurs et redémarrez l’application.

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Accédez à votre application web Azure et essayez de nouveau la nouvelle fonctionnalité. Créez un autre enregistrement d’événements.

```bash 
http://<app_name>.azurewebsites.net 
```

![Application Docker Python Flask dans Azure App Service](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gérer votre application web Azure

Accédez au portail Azure pour voir l’application web que vous avez créée.

Pour ce faire, connectez-vous au portail : [https://portal.azure.com](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **App Service**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/app-resource.png)

Vous accédez au _panneau_ de votre application web (une page du portail qui s’ouvre horizontalement).

Par défaut, le panneau de votre application web affiche la page **Présentation**. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets figurant sur le côté gauche du panneau affichent les différentes pages de configuration que vous pouvez ouvrir.

![Panneau App Service sur le portail Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/app-mgmt.png)

Ces onglets affichent les nombreuses fonctionnalités exceptionnelles que vous pouvez ajouter à votre application web. La liste suivante fournit quelques exemples des possibilités :

* Mapper un nom DNS personnalisé
* Lier un certificat SSL personnalisé
* Configurer le déploiement continu
* Montée en puissance et augmentation de la taille des instances
* Ajouter une authentification utilisateur


