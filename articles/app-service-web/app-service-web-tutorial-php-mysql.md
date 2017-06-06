---
title: "Créer une application web PHP et MySQL dans Azure | Microsoft Docs"
description: "Découvrez comment faire fonctionner une application PHP dans Azure en établissant une connexion à une base de données MySQL dans Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/05/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3ad716fab4f5084c38c83f4bc90a616856666b38
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Créer une application web PHP et MySQL dans Azure
Ce didacticiel vous montre comment créer une application web PHP dans Azure et comment la connecter à une base de données MySQL. Une fois terminé, vous disposerez d’une application [Laravel](https://laravel.com/) s’exécutant dans [Azure App Service Web Apps](app-service-web-overview.md).

![Application PHP exécutée dans Azure App Service](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d’une base de données MySQL dans Azure
> * Connexion d’une application PHP à MySQL
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gestion de l’application dans le portail Azure

## <a name="prerequisites"></a>Composants requis

Avant d’exécuter cet exemple, tenez compte des conditions préalables suivantes :

1. [Téléchargement et installation de GIT](https://git-scm.com/)
1. [Téléchargement et installation de PHP 5.6.4 ou version ultérieure](http://php.net/downloads.php)
1. [Téléchargement et installation de Composer](https://getcomposer.org/doc/00-intro.md)
1. Activation des extensions PHP suivantes requises par Laravel : OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
1. [Téléchargement, installation et démarrage de MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Téléchargement et installation de l’interface Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-local-mysql"></a>Préparation du MySQL local

Dans cette étape, vous allez créer une base de données dans votre serveur MySQL local qui vous sera utile dans ce didacticiel.

### <a name="connect-to-mysql-server"></a>Connexion au serveur MySQL
Dans une fenêtre de terminal, connectez-vous à votre serveur MySQL local.

```bash
mysql -u root -p
```

Si vous êtes invité à entrer un mot de passe, tapez le mot de passe du compte `root`. Si vous avez oublié votre mot de passe de compte racine, consultez [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL : réinitialisation du mot de passe racine).

Si la commande est exécutée correctement, votre serveur MySQL est déjà en cours d’exécution. Dans le cas contraire, assurez-vous que votre serveur MySQL local est démarré en suivant ces [étapes consécutives à l’installation de MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Créer une base de données

Dans l’invite `mysql`, créez une base de données.

```sql
CREATE DATABASE sampledb;
```

Quittez votre connexion au serveur en tapant `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-local-php-app"></a>Création d’une application PHP locale
Dans cette étape, vous allez créer un exemple d’application Laravel, configurer sa connexion à la base de données et l’exécuter localement. 

### <a name="clone-the-sample"></a>Clonage de l’exemple

Ouvrez la fenêtre de terminal et `cd` dans un répertoire de travail.  

Exécutez les commandes suivantes pour cloner l’exemple de référentiel. 

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` dans votre répertoire cloné et installez les packages requis.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configuration de la connexion MySQL

À la racine du référentiel, créez un fichier _.env_ et copiez-y les variables suivantes. Remplacez l’espace réservé  _&lt;root_password >_ par le mot de passe de l’utilisateur racine.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

> [!NOTE]
> Pour en savoir plus sur la manière dont Laravel utilise ce fichier _.env_, consultez [Configuration de l’environnement Laravel](https://laravel.com/docs/5.4/configuration#environment-configuration).
>
>

### <a name="run-the-sample"></a>Exécution de l'exemple

Exécutez les [migrations de base de données Laravel](https://laravel.com/docs/5.4/migrations) pour créer les tables requises par votre application. Pour voir quelles tables sont créées dans les migrations, consultez le répertoire _database/migrations_ dans votre référentiel Git.

```bash
php artisan migrate
```

Générez une nouvelle clé d’application Laravel.

```bash
php artisan key:generate 
```

Exécutez l’application.

```bash
php artisan serve
```

Dans un navigateur, accédez à `http://localhost:8000`. Ajoutez quelques tâches dans la page.

![PHP se connecte correctement à MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Pour arrêter PHP à tout moment, tapez `Ctrl`+`C` dans le terminal. 

## <a name="create-production-mysql-in-azure"></a>Création d’un MySQL de production dans Azure

Dans cette étape, vous allez créer une base de données MySQL dans [Azure Database pour MySQL (Version préliminaire)](/azure/mysql). Ensuite, vous configurerez votre application PHP pour vous connecter à cette base de données.

### <a name="log-in-to-azure"></a>Connexion à Azure

Vous allez maintenant utiliser Azure CLI 2.0 dans une fenêtre de terminal pour créer les ressources nécessaires pour héberger votre application PHP dans Azure App Service. Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran. 

```azurecli-interactive 
az login 
``` 

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les applications web, les bases de données et les comptes de stockage sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé dans la région Europe du Nord :

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```

Pour connaître les valeurs que vous pouvez utiliser pour `--location`, utilisez la commande [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Création d’un serveur MySQL

Créez un serveur Azure Database pour MySQL (version préliminaire) avec la commande [az mysql server create](/cli/azure/mysql/server#create).

Dans la commande suivante, indiquez le nom unique de votre propre serveur MySQL là où se trouve l’espace réservé _&lt;mysql_server_name>_. Ce nom fait partie du nom d’hôte de votre serveur MySQL, `<mysql_server_name>.database.windows.net`, et doit donc être globalement unique. De même, remplacez _&lt;admin_user>_ et _&lt;admin_password>_ par vos propres valeurs.

```azurecli-interactive
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --user <admin_user> \
    --password <admin_password>
```

Lorsque le serveur MySQL est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "<admin_user>",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configuration d’un pare-feu de serveur

Créez une règle de pare-feu pour votre serveur MySQL afin d’autoriser les connexions client à l’aide de la commande [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli-interactive
az mysql server firewall-rule create \
    --name allIPs \
    --server <mysql_server_name> \
    --resource-group myResourceGroup \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure Database pour MySQL (version préliminaire) ne permet pas encore les connexions uniquement à partir des services Azure. Étant donné que les adresses IP sont affectées dynamiquement dans Azure, il est préférable d’activer toutes les adresses IP pour le moment. Le service est pour le moment disponible en version préliminaire, mais de meilleures méthodes de sécurisation de votre base de données seront bientôt activées.
>
>

### <a name="connect-to-production-mysql-server"></a>Connexion au serveur de production MySQL

Dans la fenêtre de terminal, connectez-vous au serveur MySQL dans Azure. Utilisez la valeur spécifiée précédemment pour_&lt;admin_user>_ et _&lt;mysql_server_name>_.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>Création d’une base de données de production

Dans l’invite `mysql`, créez une base de données.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Création d’un utilisateur avec des autorisations

Créez un utilisateur de base de données et accordez-lui tous les privilèges dans la base de données `sampledb`. Remplacez les espaces réservés _&lt;phpapp_user>_ et _&lt;phpapp_password>_ par votre propre nom d’application unique.

```sql
CREATE USER '<phpapp_user>' IDENTIFIED BY '<phpapp_password>'; 
GRANT ALL PRIVILEGES ON sampledb.* TO '<phpapp_user>';
```

Quittez votre connexion au serveur en tapant `quit`.

```sql
quit
```

## <a name="connect-app-to-production-mysql"></a>Connexion de l’application au MySQL de production

Dans cette étape, vous allez connecter votre application PHP à la base de données MySQL que vous venez de créer dans Azure Database pour MySQL (version préliminaire). 

<a name="devconfig"></a>
### <a name="configure-the-connection"></a>Configuration de la connexion 

À la racine du référentiel, créez un fichier _.env.production_ et copiez-y les variables suivantes. Remplacez les espaces réservés _&lt;mysql_server_name>_, _&lt;phpapp_user>_ et _&lt;phpapp_password>_.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=<phpapp_user>@<mysql_server_name>
DB_PASSWORD=<phpapp_password>
```

Enregistrez vos modifications.

### <a name="test-the-application"></a>Test de l'application

Exécutez les migrations de base de données Laravel avec _.env.production_ comme fichier d’environnement pour créer les tables dans votre base de données MySQL dans Azure Database pour MySQL (version préliminaire).

```bash
php artisan migrate --env=production --force
```

_.env.production_ n’a pas encore de clé d’application valide. Générez-en une pour lui dans le terminal. 

```bash
php artisan key:generate --env=production --force
```

Exécutez l’exemple d’application avec _.env.production_ comme fichier d’environnement.

```bash
php artisan serve --env=production
```

Dans un navigateur, accédez à `http://localhost:8000`. Si la page se charge sans erreur, votre application PHP se connecte à la base de données MySQL dans Azure. 

Ajoutez quelques tâches dans la page.

![PHP se connecte correctement à Azure Database pour MySQL (version préliminaire)](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Pour arrêter PHP à tout moment, tapez `Ctrl`+`C` dans le terminal. 

### <a name="secure-sensitive-data"></a>Données sensibles sécurisées

Vous devez vous assurer que les données sensibles contenues dans _.env.production_ ne sont pas validées dans Git.

Pour ce faire, ouvrez _.gitignore_ à partir du référentiel racine et ajoutez le nom de fichier dans une nouvelle ligne :

```
.env.production
```

Enregistrez vos modifications, puis validez vos modifications dans Git.

```bash
git add .gitignore
git commit -m "keep sensitive data out of git"
```

Vous apprendrez ultérieurement à configurer les variables d’environnement dans App Service pour vous connecter à votre base de données dans Azure Database pour MySQL (version préliminaire). Vous n’avez donc pas besoin d’un fichier `.env` dans App Service. 

## <a name="deploy-php-app-to-azure"></a>Déploiement de l’application PHP dans Azure
Dans cette étape, vous allez déployer votre application PHP connectée à MySQL dans Azure App Service.

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

Créez un plan App Service avec la commande [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

L’exemple suivant crée un plan App Service nommé _myAppServicePlan_ en indiquant le niveau tarifaire **Gratuit** :

```azurecli-interactive
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
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
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

Dans la commande suivante, remplacez l’espace réservé _&lt;appname>_ par le nom unique de votre propre application. Ce nom unique est utilisé dans le nom de domaine par défaut de l’application web. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous pouvez ultérieurement mapper toute entrée DNS personnalisée vers l’application web avant de l’exposer à vos utilisateurs. 

```azurecli-interactive
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

### <a name="set-the-php-version"></a>Définition de la version PHP

Définissez la version PHP requise par votre application en utilisant la commande [az appservice web config update](/cli/azure/appservice/web/config#update).

La commande suivante définit la version PHP sur _7.0_.

```azurecli-interactive
az appservice web config update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>Configuration des paramètres de la base de données

Comme indiqué précédemment, vous pouvez vous connecter à votre base de données Azure MySQL en utilisant des variables d’environnement dans App Service.

Dans App Service, vous définissez les variables d’environnement en tant que _paramètres d’application_ à l’aide de la commande [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

La commande suivante vous permet de configurer les paramètres d’application `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` et `DB_PASSWORD`. Remplacez les espaces réservés _&lt;appname>_, _&lt;mysql_server_name>_, _&lt;phpapp_user>_ et _&lt;phpapp_password>_.

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="<phpapp_user>@<mysql_server_name>" DB_PASSWORD="<phpapp_password>"
```

Vous pouvez utiliser la méthode PHP [getenv()](http://www.php.net/manual/function.getenv.php) pour accéder aux paramètres. Votre code Laravel utilise un wrapper [env()](https://laravel.com/docs/5.4/helpers#method-env) sur le PHP `getenv()`. Par exemple, votre configuration MySQL dans _config/database.php_ ressemble à ceci :

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Configuration des variables d’environnement Laravel

Tout comme sur votre ordinateur local, Laravel requiert une clé d’application dans App Service. Vous pouvez également la configurer avec les paramètres d’application.

Utilisez `php artisan` pour générer une nouvelle clé d’application sans l’enregistrer dans _.env_.

```bash
php artisan key:generate --show
```

Définissez la clé d’application dans votre application web App Service en utilisant la commande [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). Remplacez les espaces réservés _&lt;appname>_ et _&lt;outputofphpartisankey:generate>_.

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

> [!NOTE]
> `APP_DEBUG="true"` indique à Laravel de renvoyer les informations de débogage si l’application web déployée rencontre des erreurs. Lors de l’exécution d’une application de production, vous devez plutôt définir ce paramètre sur `false` pour améliorer la sécurité.
>
>

### <a name="set-the-virtual-application-path"></a>Définition du chemin d’accès de l’application virtuelle

Définissez le chemin d’accès de l’application virtuelle pour votre application web. Cette étape est requise car le [cycle de vie de l’application Laravel](https://laravel.com/docs/5.4/lifecycle) commence dans le répertoire _public_ et non pas dans le répertoire racine de votre application. Les autres infrastructures PHP dont le cycle de vie démarre dans le répertoire racine peuvent fonctionner sans configuration manuelle du chemin d’accès à l’application virtuelle.

Vous définissez le chemin d’accès à l’application virtuelle en utilisant la commande [az resource update](/cli/azure/resource#update). Remplacez l’espace réservé _&lt;appname>_.

```bash
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

> [!NOTE]
> Par défaut, Azure App Service pointe le chemin d’accès à l’application virtuelle (_/_) vers le répertoire racine des fichiers d’application déployés (_sites\wwwroot_). 
>
>

### <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

Pour les sites FTP et Git locaux, il est nécessaire de disposer d’un utilisateur de déploiement configuré sur le serveur pour authentifier votre déploiement. 

> [!NOTE] 
> Un utilisateur de déploiement est requis pour les déploiements FTP et Git en local sur une application web. Le nom d’utilisateur et le mot de passe sont liés aux comptes, par conséquent, ils sont différents de vos informations d’identification de l’abonnement Azure.

Si vous avez déjà créé un nom d’utilisateur et un mot de passe de déploiement, vous pouvez utiliser la commande suivante pour afficher le nom d’utilisateur :

```azurecli-interactive
az appservice web deployment user show
```

Si vous ne disposez pas déjà d’un utilisateur de déploiement, exécutez la commande [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) pour créer vos informations d’identification de déploiement. 

```azurecli-interactive
az appservice web deployment user set \
    --user-name <username> \
    --password <minimum-8-char-capital-lowercase-number>
```

Le nom d’utilisateur doit être unique et le mot de passe doit être fort. Si vous obtenez une erreur `'Conflict'. Details: 409`, modifiez le nom d’utilisateur. Si vous obtenez une erreur `'Bad Request'. Details: 400`, utilisez un mot de passe plus fort.

Vous ne devez créer cet utilisateur de déploiement qu’une fois. Vous pouvez l’utiliser pour tous vos déploiements Azure.

Enregistrez le nom d’utilisateur et le mot de passe, car vous les utiliserez ultérieurement lors du déploiement de l’application.

### <a name="configure-local-git-deployment"></a>Configurer le déploiement Git local 

Vous pouvez déployer votre application dans Azure App Service de plusieurs façons, notamment FTP, Git local ainsi que GitHub, Visual Studio Team Services et Bitbucket. 

Utilisez la commande [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) pour configurer l’accès Git local à l’application web Azure. 

```azurecli-interactive
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

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application PHP. Le mot de passe que vous avez fourni précédemment dans le cadre de la création de l’utilisateur du déploiement vous sera demandé. 

```bash
git push azure master
```

Au cours du déploiement, Azure App Service communique sa progression avec Git.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
``` 

> [!NOTE]
> Vous remarquerez peut-être que le processus de déploiement installe les packages [Composer](https://getcomposer.org/) à la fin. App Service n’exécute pas ces automatisations pendant le déploiement par défaut. Cet exemple de référentiel possède donc trois fichiers supplémentaires dans son répertoire racine pour l’activer : 
>
> - `.deployment` : ce fichier indique à App Service d’exécuter `bash deploy.sh` en tant que script de déploiement personnalisé.
> - `deploy.sh` : le script de déploiement personnalisé. Si vous examinez le fichier, vous verrez qu’il exécute `php composer.phar install` après `npm install`. 
> - `composer.phar` - Le Gestionnaire de package Composer.
>
> Vous pouvez utiliser cette approche pour ajouter une étape à votre déploiement Git sur App Service. Pour plus d'informations, consultez le [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>
>

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure

Accédez à `http://<app_name>.azurewebsites.net` et ajoutez quelques tâches à la liste. 

![Application PHP exécutée dans Azure App Service](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

**Félicitations !** Vous exécutez une application PHP orientée données dans Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Mettre à jour le modèle de données et redéployer

Dans cette étape, vous allez apporter certaines modifications au modèle de données `task` et publier vos modifications dans Azure.

Pour le scénario des tâches, vous voulez modifier votre application afin que vous puissiez marquer une tâche comme terminée. 

### <a name="add-a-column"></a>Ajout d’une colonne

Dans le terminal, assurez-vous que vous êtes à la racine du référentiel Git, puis générez une nouvelle migration de base de données pour la table `tasks`.

```bash
php artisan make:migration add_complete_column --table=tasks
```

Cette commande affiche le nom du fichier de migration qui est généré. Recherchez ce fichier dans _database/migrations_ et ouvrez-le dans un éditeur de texte.

Remplacez la méthode up() par le code suivant :

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Ce code ajoute une colonne booléenne dans la table `tasks` nommée `complete`.

Remplacez la méthode down() par le code suivant pour l’action de restauration :

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Dans le terminal, exécutez les migrations de base de données Laravel localement pour apporter la modification dans votre base de données locale.

```bash
php artisan migrate
```

Selon la [convention d’affectation de noms Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), le modèle `Task` (voir _app/Task.php_) est mappé à la table `tasks` par défaut. Vous avez donc terminé la mise à jour du modèle de données.

### <a name="update-application-logic"></a>Mise à jour de la logique d’application

Ouvrez _routes/web.php_. L’exemple d’application définit ici ses itinéraires et sa logique métier.

À la fin du fichier, ajoutez un itinéraire avec le code suivant :

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Ce code effectue une simple mise à jour de votre modèle de données en basculant la valeur de `complete`.

### <a name="update-the-view"></a>Mise à jour de la vue

Ouvrez _resources/views/tasks.blade.php_. Recherchez la balise d’ouverture `<tr>` et remplacez-la par :

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Cela modifie la couleur de la ligne selon que la tâche est terminée ou non.

La ligne suivante contient le code suivant :

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Remplacez la ligne entière par le code suivant :

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Ce code ajoute le bouton Envoyer qui fait référence à l’itinéraire défini précédemment.

### <a name="test-your-changes-locally"></a>Tester vos modifications en local

À partir du répertoire racine de votre référentiel Git, exécutez à nouveau le serveur de développement.

```bash
php artisan serve
```

Accédez à `http://localhost:8000` dans un navigateur et cliquez sur la case à cocher pour voir l’évolution de l’état de la tâche.

![Case à cocher ajoutée à la tâche](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Dans le terminal, exécutez les migrations de base de données Laravel avec la chaîne de connexion de production pour apporter la modification dans votre base de données de production au sein d’Azure.

```bash
php artisan migrate --env=production --force
```

Validez toutes les modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Une fois l’exécution de `git push` terminée, accédez à votre application web Azure et essayez de nouveau la nouvelle fonctionnalité.

![Modifications du modèle et de la base de données publiées dans Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

> [!NOTE]
> Si vous avez ajouté des tâches précédemment, vous pouvez toujours les visualiser. Les mises à jour que vous appliquez au schéma de données n’affectent pas vos données existantes.
>
>

## <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic 

Pendant l’exécution de votre application PHP dans Azure App Service, vous pouvez acheminer les journaux de la console directement vers votre terminal. De cette façon, vous pouvez obtenir les mêmes messages de diagnostic pour vous aider à déboguer les erreurs d’application.

Pour démarrer la diffusion en continu de journaux, utilisez la commande [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli-interactive 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

Une fois que la diffusion a démarré, actualisez votre application web Azure dans le navigateur pour générer un trafic web. Vous devriez maintenant voir les journaux de la console dirigés vers votre terminal.

Pour arrêter la diffusion de journaux à tout moment, tapez `Ctrl`+`C`. 

> [!TIP]
> Une application PHP peut utiliser la commande [error_log()](http://php.net/manual/function.error-log.php) pour envoyer le résultat vers la console. L’exemple d’application utilise cette approche dans _app/Http/routes.php_.
>
> [Laravel utilise Monolog](https://laravel.com/docs/5.4/errors) comme fournisseur de journalisation, de la même manière qu’une infrastructure web. Pour savoir comment faire en sorte que Monolog envoie les messages vers la console, consultez [PHP: How to use monolog to log to console (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out) (PHP : utilisation de Monolog pour envoyer le journal vers la console (php://out)).
>
>

## <a name="manage-your-azure-web-app"></a>Gérer votre application web Azure

Accédez au portail Azure pour voir l’application web que vous avez créée.

Pour ce faire, connectez-vous au portail : [https://portal.azure.com](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **App Service**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Vous accédez au _panneau_ de votre application web (une page du portail qui s’ouvre horizontalement).

Par défaut, le panneau de votre application web affiche la page **Présentation**. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets figurant sur le côté gauche du panneau affichent les différentes pages de configuration que vous pouvez ouvrir.

![Panneau App Service sur le portail Azure](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

Ces onglets affichent les nombreuses fonctionnalités exceptionnelles que vous pouvez ajouter à votre application web. La liste suivante fournit quelques exemples des possibilités :

* Mapper un nom DNS personnalisé
* Lier un certificat SSL personnalisé
* Configurer le déploiement continu
* Montée en puissance et augmentation de la taille des instances
* Ajouter une authentification utilisateur

## <a name="clean-up-resources"></a>Suppression des ressources
 
Si vous n’avez pas besoin de ces ressources pour un autre didacticiel (voir [Étapes suivantes](#next)), vous pouvez les supprimer en exécutant la commande suivante : 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Création d’une base de données MySQL dans Azure
> * Connexion d’une application PHP à MySQL
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gestion de l’application dans le portail Azure

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à une application web.

> [!div class="nextstepaction"] 
> [Mapper un nom DNS personnalisé existant à des applications web Azure](app-service-web-tutorial-custom-domain.md)

