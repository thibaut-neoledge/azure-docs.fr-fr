---
title: "Utiliser .NET Core dans une application web sur Linux | Microsoft Docs"
description: Utilisez .NET Core dans une application web sur Linux.
keywords: azure app service, application web, dotnet, core, linux, oss
services: app-service
documentationCenter: 
authors: michimune, rachelappel
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: aelnably;wesmc;mikono;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 3b15d6645b988f69f1f05b27aff6f726f34786fc
ms.openlocfilehash: cbcc43e04e58894b6505fdf6c48c7792343049b9
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---

# <a name="use-net-core-in-an-azure-web-app-on-linux"></a>Utiliser .NET Core dans une application web Azure sur Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Une [application web](https://docs.microsoft.com/azure/app-service-web/app-service-linux-intro) sur Linux fournit un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux. Ce didacticiel contient des instructions détaillées montrant comment créer une application [.NET Core](https://docs.microsoft.com/aspnet/core/) sur une application web Azure sur Linux. 

![Application web sous Linux][10]

Vous pouvez suivre les étapes ci-dessous en utilisant un ordinateur Mac, Windows ou Linux.

## <a name="prerequisites"></a>Composants requis ##

Pour suivre ce didacticiel : 

* Installez le [kit de développement logiciel (SDK) .NET Core](https://www.microsoft.com/net/download/core).
* Installez [Git](https://git-scm.com/downloads).

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-local-net-core-application"></a>Créer une application .NET Core locale ##

Démarrez une nouvelle session de terminal. Créez un répertoire nommé `hellodotnetcore` et remplacez le répertoire actuel par ce dernier. Tapez ensuite la commande suivante : 

```
dotnet new web
``` 

  Cette commande crée trois fichiers (*hellodotnetcore.csproj*, *Program.cs* et *Startup.cs*) et un dossier vide (*wwwroot/*) sous le répertoire actuel. Le contenu du fichier `.csproj` doit ressembler à ce qui suit : 

```xml
  <!-- Empty lines are omitted. -->

  <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
        <TargetFramework>netcoreapp1.1</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
        <Folder Include="wwwroot\" />
        </ItemGroup>
        <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore" Version="1.1.2" />
        </ItemGroup>
  </Project>
```

Étant donné que cette application est une application web, une référence à un package ASP.NET Core a été automatiquement ajoutée au fichier *hellodotnetcore.csproj*. Le numéro de version du package est défini en fonction de l’infrastructure choisie. Cet exemple fait référence à ASP.NET Core version 1.1.2, car .NET Core 1.1 est utilisé.

## <a name="build-and-test-the-application-locally"></a>Générer et tester l’application localement ##

Vous pouvez générer et exécuter votre application .NET Core avec la commande `dotnet restore` suivie de la commande `dotnet run`, comme indiqué ici :

```
dotnet restore
dotnet run
```


Lorsque l’application démarre, elle affiche un message indiquant que l’application écoute les demandes entrantes au niveau d’un port. 

```bash
Hosting environment: Production
Content root path: C:\hellodotnetcore
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Testez-la en accédant à `http://localhost:5000/` avec votre navigateur. Si tout fonctionne correctement, vous voyez « Hello World! » comme texte de résultat.

![Tester avec un navigateur][7]

## <a name="create-a-net-core-app-in-the-azure-portal"></a>Créer une application .NET Core dans le portail Azure ##

Vous devez d’abord créer une application web vide. Connectez-vous au [portail Azure](https://portal.azure.com/) et créez une [application web sur Linux](https://portal.azure.com/#create/Microsoft.AppSvcLinux).

![Création d’une application web][1]

Lorsque la page **Créer** s’ouvre, fournissez des détails sur votre application web :

![Choix d’une pile d’exécution .NET Core][2]

Utilisez le tableau suivant comme guide pour remplir la page **Créer**, puis sélectionnez **OK** et **Créer** pour créer l’application.

| Paramètre      | Valeur suggérée  | Description                                        |
| ------------ | ---------------- | -------------------------------------------------- |
| Nom de l’application | hellodotnetcore  | Nom de votre application. Ce nom doit être unique. |
| Abonnement | Choisir un abonnement existant | Abonnement Azure. |
| Groupe de ressources | myResourceGroup |  Groupe de ressources Azure qui hébergera l’application web. |
| Plan App Service | Nom de plan App Service existant |  Plan App Service.  |
| Configurer le conteneur | .NET Core 1.1 | Type de conteneur pour cette application web : intégré, Docker ou registre privé. |
| Source d’image  | Intégration  |  Source de l’image. |
| Pile d’exécution  | .NET Core 1.1  | Version et pile d’exécution.  |

## <a name="deploy-your-application-via-git"></a>Déployer votre application via Git ##

Utilisez Git pour déployer l’application .NET Core sur une application web Azure App Service sur Linux.

Un déploiement Git est déjà configuré sur la nouvelle application web Azure. Vous trouverez l’URL de déploiement Git en suivant l’URL suivante après avoir inséré le nom de votre application web :

```https://{your web app name}.scm.azurewebsites.net/api/scm/info```

L’URL Git a la forme suivante, selon le nom de votre application web :

```https://{your web app name}.scm.azurewebsites.net/{your web app name}.git```

Exécutez les commandes suivantes pour déployer l’application locale sur votre application web Azure : 
 
```bash
git init
git remote add azure <Git deployment URL from above>
git add *.csproj *.cs
git commit -m "Initial deployment commit"
git push azure master
```

Vous n’avez pas besoin d’envoyer de fichiers sous les répertoires *bin/* ou *obj/*, car votre application est générée dans le cloud lorsque les fichiers source de l’application sont envoyés à Azure. Une fois le processus de génération terminé, les fichiers binaires sont copiés dans le répertoire de l’application à l’emplacement */home/site/wwwroot/*.

Vérifiez que les opérations de déploiement à distance réussissent. L’envoi des opérations peut prendre un certain temps, car la résolution de package et le processus de génération s’exécutent dans le cloud. Plusieurs messages d’état s’afficheront, y compris ceux indiquant que les fichiers ont été copiés. Le résultat doit être semblable à ce qui suit :

```bash
/* some output has been removed for brevity */
remote: Copying file: 'System.Net.Websockets.dll' 
remote: Copying file: 'System.Runtime.CompilerServices.Unsafe.dll' 
remote: Copying file: 'System.Runtime.Serialization.Primitives.dll' 
remote: Copying file: 'System.Text.Encodings.Web.dll' 
remote: Copying file: 'hellodotnetcore.deps.json' 
remote: Copying file: 'hellodotnetcore.dll' 
remote: Omitting next output lines...
remote: Finished successfully.
remote: Running post deployment commands...
remote: Deployment successful.
To https://hellodotnetcore.scm.azurewebsites.net/
 * [new branch]           master -> master

```

Une fois le déploiement terminé, redémarrez votre application web pour que le déploiement prenne effet. Pour ce faire, accédez au portail Azure, puis à la page **Vue d’ensemble** de votre application web. Sélectionnez le bouton **Redémarrer** sur la page. Quand une fenêtre contextuelle s’affiche, sélectionnez **Oui** pour confirmer. Vous pouvez ensuite parcourir votre application web, comme illustré ici :

![Exploration de l’application .NET Core déployée sur Azure App Service sur Linux][10]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes
* [FAQ de l’application web Azure App Service sur Linux](./app-service-linux-faq.md)

[1]: ./media/app-service-linux-using-dotnetcore/top-level-create.png
[2]: ./media/app-service-linux-using-dotnetcore/dotnet-new-webapp.png
[7]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-local.png
[10]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-azure.png

