---
title: "Créer votre première application web Python dans Azure en 5 minutes | Microsoft Docs"
description: "Déployez votre premier Python Hello World dans l’application web App Service en quelques minutes."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: df34052acc401fb5bb1e3f808c649c0ea0bcf33c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017

---
# <a name="create-a-python-application-on-web-app"></a>Créer une application Python sur une application web

Ce didacticiel de démarrage rapide vous guide dans le développement et le déploiement d’une application Python dans Azure. Nous allons exécuter l’application à l’aide d’un service Azure App, puis créer et configurer une nouvelle application web à l’aide de l’interface Azure CLI. Nous allons ensuite utiliser GIT pour déployer notre application Python sur Azure.

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

Vous pouvez suivre les étapes ci-dessous en utilisant un ordinateur Mac, Windows ou Linux. L’exécution des étapes ci-dessous ne devrait prendre que 5 minutes environ.

## <a name="prerequisites"></a>Composants requis

Avant d’exécuter cet exemple, tenez compte des conditions préalables suivantes :

1. [Téléchargement et installation de GIT](https://git-scm.com/)
1. [Téléchargement et installation de Python](https://www.python.org/downloads/)
1. Téléchargement et installation de l’interface [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="download-the-sample"></a>Téléchargez l’exemple

Clonez le référentiel de l’exemple d’application Hello World sur votre ordinateur local.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

> [!TIP]
> Vous pouvez également [télécharger l’exemple](https://github.com/Azure-Samples/Python-docs-hello-world/archive/master.zip) sous la forme d’un fichier zip et l’extraire.

Passez au répertoire qui contient l’exemple de code.

```bash
cd Python-docs-hello-world
```

## <a name="run-the-app-locally"></a>Exécutez l’application localement.

Exécutez l’application localement en ouvrant une fenêtre de terminal et en utilisant la ligne de commande `Python` pour que l’exemple lance le serveur web Python.

```bash
python main.py
```

Ouvrez un navigateur web et accédez à l’exemple.

```bash
http://localhost:5000
```

Vous pouvez voir sur la page le message **Hello World** de l’exemple d’application.

![localhost-hello-world-in-browser](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

Dans la fenêtre de terminal, appuyez sur **Ctrl + C** pour quitter le serveur web.

## <a name="log-in-to-azure"></a>Connexion à Azure

Nous allons maintenant utiliser l’interface Azure CLI 2.0 dans une fenêtre de terminal pour créer les ressources nécessaires pour héberger notre application Python dans Azure. Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>Configurer un utilisateur de déploiement

Pour les sites FTP et Git locaux, il est nécessaire de disposer d’un utilisateur de déploiement configuré sur le serveur pour authentifier votre déploiement. La création d’un utilisateur de déploiement est une opération de configuration unique. Notez le nom d’utilisateur et le mot de passe, car ils seront utilisés lors d’une étape à suivre.

> [!NOTE]
> Un utilisateur de déploiement est requis pour les déploiements FTP et Git en local sur une application web.
> Le `username` et `password` sont liés aux comptes et sont par conséquent différents de vos informations d’identification de l’abonnement Azure. **Ces informations d’identification ne doivent être créées qu’une seule fois**.
>

Utilisez la commande [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) pour créer vos informations d’identification au niveau des comptes.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les applications web, les bases de données et les comptes de stockage sont déployées et gérées.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>Créez un plan Azure App Service

Créez un plan App Service avec la commande [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

L’exemple suivant crée un plan App Service nommé `quickStartPlan` en appliquant le niveau tarifaire **Gratuit**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

Lorsque le plan App Service est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant.

```json
{
"appServicePlanName": "quickStartPlan",
"geoRegion": "North Europe",
"id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
"kind": "app",
"location": "North Europe",
"maximumNumberOfWorkers": 1,
"name": "quickStartPlan",
"provisioningState": "Succeeded",
"resourceGroup": "myResourceGroup",
"sku": {
  "capacity": 0,
  "family": "F",
  "name": "F1",
  "size": "F1",
  "tier": "Free"
},
"status": "Ready",
"type": "Microsoft.Web/serverfarms",
}
```

## <a name="create-a-web-app"></a>Créer une application web

Maintenant qu’un plan App Service est créé, générez une application web dans le plan App Service `quickStartPlan`. L’application web nous offre un espace d’hébergement pour déployer notre code, et fournit une URL pour nous permettre d’afficher l’application déployée. Utilisez la commande [az appservice web create](/cli/azure/appservice/web#create) pour créer l’application web.

Dans la commande ci-dessous, indiquez le nom unique de votre propre application là où se trouve `<app_name>`l’espace réservé . La valeur `<app_name>` sera utilisée en tant que site DNS par défaut pour l’application web. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous pouvez ultérieurement mapper toute entrée DNS personnalisée vers l’application web avant de l’exposer à vos utilisateurs.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Une fois l’application web créée, Azure CLI affiche des informations semblables à celles de l’exemple suivant.

```json
{
  "clientAffinityEnabled": true,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "hostNames": [
    "<app_name>.azurewebsites.net"
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
  "kind": "app",
  "location": "North Europe",
  "outboundIpAddresses": "13.69.190.80,13.69.191.239,13.69.186.193,13.69.187.34",
  "resourceGroup": "myResourceGroup",
  "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "state": "Running",
  "type": "Microsoft.Web/sites",
}
```

Accédez au site pour voir votre nouvelle application web.

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-python/app-service-web-service-created.png)

Nous avons maintenant créé une application web vide dans Azure. Configurons maintenant notre application web pour utiliser Python et y déployer notre application.

## <a name="configure-to-use-python"></a>Configurer l’utilisation de Python

Utilisez la commande [az appservice web config update](/cli/azure/app-service/web/config#update) pour configurer l’application web pour utiliser la version de Python `3.4`.

> [!TIP]
> Lorsque vous définissez la version de Python ainsi, vous utilisez un conteneur par défaut fourni par la plate-forme. Si vous souhaitez utiliser votre propre conteneur reportez-vous à la référence de l’interface de ligne de commande pour la commande [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update).

```azurecli
az appservice web config update --python-version 3.4 --name <app-name> --resource-group myResourceGroup
```

## <a name="configure-local-git-deployment"></a>Configurer le déploiement Git local

Vous pouvez procéder au déploiement sur l’application web de plusieurs façons, notamment FTP, Git local ainsi que GitHub, Visual Studio Team Services et Bitbucket.

Utilisez la commande [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) pour configurer l’accès Git local à l’application web.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Copiez la sortie du terminal, car elle sera utilisée à l’étape suivante.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

Ajoutez un référentiel distant Azure dans votre référentiel Git local.

```bash
git remote add azure <paste-previous-command-output-here>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application. Le mot de passe que vous avez fourni précédemment dans le cadre de la création de l’utilisateur du déploiement vous sera demandé.

```azurecli
git push azure master
```

Au cours du déploiement, Azure App Service communiquera sa progression avec Git.

```bash
Counting objects: 18, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (16/16), done.
Writing objects: 100% (18/18), 4.31 KiB | 0 bytes/s, done.
Total 18 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '44e74fe7dd'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling python deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'main.py'
remote: Copying file: 'README.md'
remote: Copying file: 'requirements.txt'
remote: Copying file: 'virtualenv_proxy.py'
remote: Copying file: 'web.2.7.config'
remote: Copying file: 'web.3.4.config'
remote: Detected requirements.txt.  You can skip Python specific steps with a .skipPythonDeployment file.
remote: Detecting Python runtime from site configuration
remote: Detected python-3.4
remote: Creating python-3.4 virtual environment.
remote: .................................
remote: Pip install requirements.
remote: Successfully installed Flask click itsdangerous Jinja2 Werkzeug MarkupSafe
remote: Cleaning up...
remote: .
remote: Overwriting web.config with web.3.4.config
remote:         1 file(s) copied.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée à l’aide de votre navigateur web.

```bash
http://<app_name>.azurewebsites.net
```

Cette fois, la page qui affiche le message Hello World est en cours d’exécution à l’aide de notre code Python qui s’exécute en tant qu’application web Azure App Service.

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

## <a name="updating-and-deploying-the-code"></a>Mise à jour et déploiement du code

À l’aide d’un éditeur de texte local, ouvrez le fichier `main.py` dans l’application Python et effectuez une légère modification du texte contenu dans l’appel pour `return` :

```python
return 'Hello, Azure!'
```

Validez vos modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git commit -am "updated output"
git push azure master
```

Une fois le déploiement terminé, revenez à la fenêtre du navigateur ouverte dans le navigateur à l’étape de navigation vers l’application, puis cliquez sur Actualiser.

![hello-azure-in-browser](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Gérer votre nouvelle application web Azure

Accédez au portail Azure pour voir l’application web que vous venez de créer.

Pour ce faire, connectez-vous au portail : [https://portal.azure.com](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/app-service-web-get-started-python/app-service-list.png)

Vous accédez au _panneau_ de votre application web (une page du portail qui s’ouvre horizontalement).

Par défaut, le panneau de votre application web affiche la page **Présentation**. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets figurant sur le côté gauche du panneau affichent les différentes pages de configuration que vous pouvez ouvrir.

![Panneau App Service sur le portail Azure](media/app-service-web-get-started-python/app-service-detail.png)

Ces onglets affichent les nombreuses fonctionnalités exceptionnelles que vous pouvez ajouter à votre application web. La liste suivante fournit quelques exemples des possibilités :

* Mapper un nom DNS personnalisé
* Lier un certificat SSL personnalisé
* Configurer le déploiement continu
* Montée en puissance et augmentation de la taille des instances
* Ajouter une authentification utilisateur

**Félicitations !** Vous avez déployé votre première application Python dans App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Parcourez des exemples de scripts CLI d’applications web](app-service-cli-samples.md)

