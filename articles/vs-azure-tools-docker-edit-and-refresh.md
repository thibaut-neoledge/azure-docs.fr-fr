---
title: "Débogage d’applications dans un conteneur Docker local | Microsoft Docs"
description: "Découvrez comment modifier une application qui s’exécute dans un conteneur Docker local et actualiser le conteneur via la modification et l’actualisation ainsi que la définition de points d’arrêt pour le débogage"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: fcd58736d8915a61683a416fb9bf3892ba7b7bd8
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# Débogage d’applications dans un conteneur Docker local
<a id="debugging-apps-in-a-local-docker-container" class="xliff"></a>
## Vue d'ensemble
<a id="overview" class="xliff"></a>
Visual Studio Tools pour Docker fournit un moyen cohérent de développer et valider votre application localement sur un conteneur de Docker Linux.
Vous n’avez pas besoin de redémarrer le conteneur chaque fois que vous modifiez le code.
Cet article illustre comment utiliser la fonctionnalité Modifier et actualiser pour démarrer une application web ASP.NET Core dans un conteneur Docker local, apporter les modifications nécessaires, puis actualiser le navigateur pour afficher ces modifications.
Cet article vous montre également comment définir des points d’arrêt pour le débogage.

> [!NOTE]
> La prise en charge des conteneurs Windows sera proposée dans une version ultérieure
>
>

## Composants requis
<a id="prerequisites" class="xliff"></a>
Les outils suivants doivent être installés.

* [Dernière version de Visual Studio](https://www.visualstudio.com/downloads/)
* [Kit de développement logiciel (SDK) Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Pour exécuter des conteneurs de Docker localement, vous aurez besoin d’un client Docker local.
Vous pouvez utiliser la [Boîte à outils Docker](https://www.docker.com/products/docker-toolbox), qui nécessite la désactivation d’Hyper-V, ou [Docker pour Windows](https://www.docker.com/get-docker) qui utilise Hyper-V et nécessite Windows 10.

Si vous utilisez la Boîte à outils Docker, vous devrez [Configurer le client Docker](vs-azure-tools-docker-setup.md)

## 1. Créer une application web
<a id="1-create-a-web-app" class="xliff"></a>
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2. Ajouter la prise en charge Docker
<a id="2-add-docker-support" class="xliff"></a>
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 3. Modifier votre code et actualiser
<a id="3-edit-your-code-and-refresh" class="xliff"></a>
Pour effectuer une itération rapide des modifications, vous pouvez lancer votre application au sein d’un conteneur et continuer à apporter des modifications, en les observant comme vous le feriez avec IIS Express.

1. Définissez la configuration de solution sur `Debug`, puis appuyez sur **&lt;CTRL + F5 >** pour créer votre image de Docker et l’exécuter localement.

    Une fois l’image de conteneur créée et en cours d’exécution dans un conteneur Docker, Visual Studio lancera l’application web dans votre navigateur par défaut.
    Si vous utilisez le navigateur Microsoft Edge ou rencontrez des erreurs, consultez la section [Résolution des problèmes](vs-azure-tools-docker-troubleshooting-docker-errors.md) .
2. Accédez à la page À propos, à laquelle nous allons apporter nos modifications.
3. Revenez à Visual Studio et ouvrez `Views\Home\About.cshtml`.
4. Ajoutez le contenu HTML suivant à la fin du fichier et enregistrez les modifications.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Dans la fenêtre de sortie, lorsque le build .NET est terminé et que vous voyez ces lignes, revenez sur votre navigateur et actualisez la page À propos.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. Vos modifications ont été appliquées !

## 4. Déboguer à l’aide de points d’arrêt
<a id="4-debug-with-breakpoints" class="xliff"></a>
Les modifications nécessitent souvent une inspection plus poussée, en exploitant les fonctionnalités de débogage de Visual Studio

1. Revenez à Visual Studio et ouvrez `Controllers\HomeController.cs`
2. Remplacez le contenu de la méthode About() par ce qui suit :

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Définissez un point d’arrêt à gauche de la ligne `string message`....
4. Appuyez sur **&lt;F5>** pour démarrer le débogage.
5. Accédez à la page À propos de pour atteindre votre point d’arrêt.
6. Basculez sur Visual Studio pour afficher le point d’arrêt, puis inspectez la valeur du message.

   ![][2]

## Résumé
<a id="summary" class="xliff"></a>
Avec [Visual Studio 2015 Tools pour Docker](https://aka.ms/DockerToolsForVS), vous pouvez obtenir la productivité du travail local tout en conservant le réalisme de production du développement au sein d’un conteneur Docker.

## Résolution des problèmes
<a id="troubleshooting" class="xliff"></a>
[Résolution des problèmes de développement avec Docker pour Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## En savoir plus sur Docker avec Visual Studio, Windows et Azure
<a id="more-about-docker-with-visual-studio-windows-and-azure" class="xliff"></a>
* [Outils Docker pour Visual Studio](http://aka.ms/dockertoolsforvs) : développer votre code .NET Core dans un conteneur
* [Outils Docker pour Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) : créez et déployez des conteneurs Docker
* [Outils Docker pour Visual Studio Code](http://aka.ms/dockertoolsforvscode) : services linguistiques pour la modification de fichiers Docker, avec plus de scénarios E2E à venir
* [Informations sur le conteneur Windows](http://aka.ms/containers): informations sur Windows Server et Nano Server
* [Service de conteneur Azure](https://azure.microsoft.com/services/container-service/) - [Contenu du service de conteneur Azure](http://aka.ms/AzureContainerService)
* Pour plus d’exemples sur l’utilisation de Docker, consultez [Utilisation de Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) à partir de la [démonstration](https://github.com/Microsoft/HealthClinic.biz) de 2015 Connect pour [HealthClinic.biz](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Pour d’autres démarrages rapides à partir de la démonstration pour HealthClinic.biz, consultez [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)(Démarrages rapides avec les outils de développement Azure).

## Divers outils Docker
<a id="various-docker-tools" class="xliff"></a>
[D’excellents outils Docker (blog de Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## Articles intéressants
<a id="good-articles" class="xliff"></a>
[Introduction aux microservices de NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## Présentations
<a id="presentations" class="xliff"></a>
* [Steve Lasker : Visual Studio Live Las Vegas 2016 - E2E Docker](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduction à ASP.NET Core build 2016 - Démonstration Where You At](https://channel9.msdn.com/Events/Build/2016/B810)
* [Développement d’applications .NET dans des conteneurs, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

