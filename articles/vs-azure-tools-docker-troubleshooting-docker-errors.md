---
title: "Dépannage d’erreurs client Docker sur Windows avec Visual Studio | Microsoft Docs"
description: "Résolvez les problèmes d’utilisation de Visual Studio pour créer et déployer des applications web dans Docker sur Windows avec Visual Studio."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 01c10d04606e15082c8842ad87f617703a00c903
ms.openlocfilehash: cbb376dae88d39e965838de74d90158691b59f90


---

# <a name="troubleshooting-visual-studio-docker-development"></a>Résolution des problèmes de développement avec Docker pour Visual Studio

Lorsque vous travaillez avec Visual Studio Tools pour Docker Preview, vous pourriez rencontrer des problèmes en raison de la nature de la version préliminaire.
Voici quelques problèmes courants et leurs solutions.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Conteneurs Linux**

###  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Des erreurs de build se produisent lors du débogage d’une application de console ou web .NET Core.  

Cela peut être lié au non-partage du lecteur sur lequel réside le projet avec Docker pour Windows.  Vous pourriez recevoir une erreur telle que la suivante :

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Pour résoudre ce problème, cliquez avec le bouton droit sur l’icône de la barre d’état système Docker pour Windows et sélectionnez « Paramètres... ».  Cliquez sur l’onglet « Lecteurs partagés » et partagez la lettre du lecteur où se trouve le projet.

### <a name="windows-containers"></a>**Conteneurs Windows**

Les éléments suivants sont spécifiques à la résolution des problèmes lors du débogage d’applications de console et web .NET Framework dans des conteneurs Windows

### <a name="pre-requisites"></a>Conditions préalables

1. Visual Studio 2017 RC (ou version ultérieure) avec la charge de travail .NET Core et Docker (version préliminaire) installée.
2. Mise à jour anniversaire de Windows 10 avec les derniers correctifs de mise à jour Windows installés.
3. Docker pour Windows (bêta) - https://docs.docker.com/docker-for-windows/ (version 1.12.2-beta28 7813 ou version ultérieure).
4. À partir de l’icône de la barre d’état système Docker pour Windows, sélectionnez « Basculer vers les conteneurs Windows ».  Après le redémarrage de l’ordinateur, assurez-vous que ce paramètre est conservé.

### <a name="clicking-docker-debug-project-results-in-the-error--client-version-122-is-too-old--minimum-supported-api-version-is-124-please-upgrade-your-client-to-a-newer-version"></a>Le fait de cliquer sur **Docker : déboguer le projet** entraîne l’erreur « la version cliente 1.22 est trop ancienne.  La version d’API minimale prise en charge est 1.24, veuillez mettre à niveau votre client vers une version plus récente ».

La version 1.13-RC2-beta31 (9123) ou ultérieure de Docker pour Windows nécessite l’utilisation de la version « 2.1 » de Docker Compose.   Pour résoudre ce problème, remplacez toutes les occurrences de la version « 2 » dans les fichiers compose*.yml de Docker au sein du projet. Les modèles par défaut que Visual Studio ajoute au projet seront actualisés dans une prochaine version des outils. 

### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>La sortie de la console n’apparaît pas dans la fenêtre de sortie de Visual Studio pendant le débogage d’une application de console.

Il s’agit d’un problème connu avec le débogueur Visual Studio (msvsmon.exe), qui n’est actuellement pas conçu pour ce scénario.  Nous étudions actuellement la prise en charge dans une version ultérieure.  Pour afficher la sortie de l’application de console dans Visual Studio, utilisez « Docker : Démarrer le projet », qui équivaut à « Démarrer sans débogage ».

### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>Le débogage d’applications web avec la configuration de version échoue avec l’erreur (403) Interdit.

Pour contourner ce problème, ouvrez le fichier web.release.config dans la solution, puis placez en commentaire ou supprimez les lignes suivantes :

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

### <a name="when-switching-to-windows-containers-you-could-potentially-see-an-error-dialog-stating-error-response-from-daemon-io-timeout"></a>En basculant vers les conteneurs Windows, vous pourriez voir s’afficher une boîte de dialogue d’erreur indiquant « Réponse d’erreur du démon : délai d’expiration E/S ».

Ce problème dans Docker pour Windows peut être suivi à l’adresse https://github.com/docker/for-win/issues/178.


## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Conteneurs Linux**

### <a name="unable-to-validate-volume-mapping"></a>Impossible de valider le mappage de volume
Le mappage de volume est requis pour partager le code source et les fichiers binaires de votre application avec le dossier de l’application dans le conteneur.  Les mappages de volume spécifique sont contenus dans les fichiers docker-compose.dev.debug.yml et docker-compose.dev.release.yml. Au fur et à mesure que les fichiers sont modifiés sur votre ordinateur hôte, les conteneurs reflètent ces modifications dans une structure de dossiers similaire.

Pour activer le mappage de volume, ouvrez **Paramètres...** à partir de l’icône moby de la barre d’état système Docker For Windows, puis sélectionnez l’onglet **Disques partagés**.  Assurez-vous que la lettre du lecteur qui héberge votre projet et que la lettre du lecteur où se trouve %USERPROFILE% sont identiques, puis cliquez sur **Appliquer**.

Pour vérifier si le mappage de volume fonctionne, une fois que davantage de lecteurs ont été partagés, effectuez la reconstruction et cliquez sur F5 dans Visual Studio ou essayez la procédure suivante à partir d’une invite de commandes :

*Dans une invite de commandes Windows*

> [!Note]
> Cet exemple suppose que le dossier Utilisateurs se trouve sur le lecteur « C» et qu’il a été partagé.
> Mettez à jour si nécessaire si vous avez partagé un autre lecteur.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Dans le conteneur Linux*

```
/ # ls
```

Vous devriez voir une liste de répertoires dans le dossier Users/Public.
Si aucun fichier ne s’affiche et que votre dossier /c/Users/Public n’est pas vide, le mappage de volumes n’est pas configuré correctement.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Passez au répertoire wormhole pour voir le contenu du répertoire `/c/Users/Public` :

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!Note]
> Lorsque vous travaillez avec des machines virtuelles Linux, le système de fichiers du conteneur est sensible à la casse.

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Build : échec inattendu de la tâche PrepareForBuild.

Microsoft.DotNet.Docker.CommandLine.ClientException : une erreur s’est produite lors de la tentative de connexion :

Vérifiez que l’hôte Docker par défaut est en cours d’exécution. Ouvrez une invite de commandes et exécutez :

```
docker info
```

Si elle renvoie une erreur, essayez de démarrer l’application de bureau **Docker pour Windows** .  Si l’application de bureau est en cours d’exécution, l’icône **moby** de la barre d’état système doit être visible. Cliquez avec le bouton droit sur l’icône de barre d’état et ouvrez **Paramètres**.  Cliquez sur l’onglet **Réinitialiser**, puis sur **Redémarrer Docker...**.

##<a name="manually-upgrading-from-version-031-to-040"></a>Mise à niveau manuelle de la version 0.31 à 0.40


1. Sauvegarde du projet
1. Supprimez les fichiers suivants dans le projet :

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Fermez la solution et supprimez les lignes suivantes du fichier .xproj :

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Ouvrez de nouveau la solution
1. Supprimez les lignes suivantes du fichier Properties\launchSettings.json file :

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Supprimez les fichiers suivants associés à Docker du project.json dans publishOptions :

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Désinstallez la version précédente et installez Docker Tools 0.40, puis **Ajouter -> Prise en charge Docker** à partir du menu contextuel pour votre application web ASP.Net Core ou console. Cela ajoute les nouveaux artefacts Docker requis à votre projet.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Une boîte de dialogue d’erreur apparaît lorsque vous effectuez l’opération **Ajouter -> Prise en charge Docker** ou Débogage (F5) sur une application ASP.NET Core dans un conteneur

Après la désinstallation et l’installation des extensions, le cache MEF (Managed Extensibility Framework) de Visual Studio peut être endommagé. Dans ce cas, cela peut générer des boîtes de dialogue d’erreur lors de l’ajout de la prise en charge Docker et/ou de la tentative d’exécution ou du Débogage (F5) sur votre application ASP.NET Core. Pour contourner provisoirement le problème, exécutez les étapes suivantes pour supprimer et régénérer le cache MEF.

1. Fermez toutes les instances de Visual Studio
1. Ouvrez %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. Supprimez les dossiers suivants
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Ouvrez Visual Studio.
1. Réessayez le scénario



<!--HONumber=Dec16_HO2-->


