---
title: "Créer une application web .NET Core dans un conteneur Linux dans Azure | Microsoft Docs"
description: "Déployez votre première application .NET Core Hello World dans Web App for Containers en quelques minutes."
keywords: azure app service, application web, dotnet, core, linux, oss
services: app-service
documentationCenter: 
authors: cephalin
manager: syntaxc4
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: quickstart
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 3ae0f28272d70a63b74a9f249566ae789f07aad5
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---
# <a name="create-a-net-core-web-app-in-a-linux-container-in-azure"></a>Créer une application web .NET Core dans un conteneur Linux dans Azure

[Web App for Containers](app-service-linux-intro.md) est un service d’hébergement web hautement scalable qui applique automatiquement des mises à jour correctives et utilise le système d’exploitation Linux. Ce guide de démarrage rapide vous montre comment créer une application [.NET Core](https://docs.microsoft.com/aspnet/core/) sur Azure Web App for Containers. Vous allez créer l’application web à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) et utiliser Git pour déployer le code .NET Core dans l’application web.

![Exemple d’application s’exécutant dans Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Vous pouvez suivre les étapes ci-dessous en utilisant un ordinateur Mac, Windows ou Linux. 

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide :

* [Installez Git](https://git-scm.com/)
* [Installer le kit de développement logiciel (SDK) .NET Core](https://www.microsoft.com/net/download/core)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-app-locally"></a>Créer l’application en local ##

Dans une fenêtre de terminal sur votre machine, créez un répertoire nommé `hellodotnetcore` et remplacez le répertoire actuel par ce dernier. 

```bash
md hellodotnetcore
cd hellodotnetcore
``` 

Créez une application web .NET Core.

```bash
dotnet new web
``` 

## <a name="run-the-app-locally"></a>Exécutez l’application localement.

Restaurez les packages NuGet et exécutez l’application.

```bash
dotnet restore
dotnet run
```

Ouvrez un navigateur web et accédez à l’application à l’adresse `http://localhost:5000`.

Vous voyez apparaître sur la page le message **Hello World** de l’exemple d’application.

![Tester avec un navigateur](media/quickstart-dotnetcore/dotnet-browse-local.png)

Dans la fenêtre de terminal, appuyez sur **Ctrl + C** pour quitter le serveur web. Initialisez un dépôt Git pour le projet .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)] 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)] 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)] 

## <a name="create-a-web-app"></a>Créer une application web

Créez une [application web](../app-service-web-overview.md) dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp#create). N’oubliez pas de remplacer `<app name>` par un nom d’application unique.

Dans la commande suivante, le runtime est défini sur `DOTNETCORE|1.1`. Pour afficher tous les runtimes pris en charge, exécutez [az webapp list-runtimes](/cli/azure/webapp#list-runtimes).

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "DOTNETCORE|1.1" --deployment-local-git
```

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-result.md)] 

![Page d’application web vide](media/quickstart-dotnetcore/dotnet-browse-created.png)

Vous avez créé une application web vide dans un conteneur Linux avec le déploiement Git activé.

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée à l’aide de votre navigateur web.

```bash
http://<app_name>.azurewebsites.net
```

L’exemple de code Node.js s’exécute dans une application web Azure App Service.

![Exemple d’application s’exécutant dans Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Félicitations !** Vous avez déployé votre première application Node.js dans App Service.

## <a name="update-and-redeploy-the-code"></a>Mettre à jour et redéployer le code

Dans le répertoire local, ouvrez le fichier _Startup.cs_. Apportez une petite modification au texte de l’appel de méthode `context.Response.WriteAsync` :

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Validez vos modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git commit -am "updated output"
git push azure master
```

Une fois le déploiement terminé, revenez à la fenêtre du navigateur ouverte à l’étape **Accéder à l’application**, puis cliquez sur Actualiser.

![Mise à jour de l’exemple d’application s’exécutant dans Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-web-app"></a>Gérer votre nouvelle application web Azure

Accédez au <a href="https://portal.azure.com" target="_blank">Portail Azure</a> pour gérer l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/quickstart-dotnetcore/portal-app-service-list.png)

Vous voyez apparaître la page Vue d’ensemble de votre application web. Ici, vous pouvez également des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). 

![Page App Service du Portail Azure](media/quickstart-dotnetcore/portal-app-overview.png)

Le menu de gauche fournit différentes pages vous permettant de configurer votre application. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web .NET Core et SQL Database dans Azure Web App for Containers](tutorial-dotnetcore-sqldb-app.md)

