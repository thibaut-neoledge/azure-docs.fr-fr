---
title: "Dépannage des erreurs du client Docker sur Windows avec Visual Studio | Microsoft Docs"
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
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-visual-studio-docker-development"></a>Résoudre des problèmes de développement avec Docker pour Visual Studio

Lorsque vous travaillez avec Visual Studio Tools pour Docker Preview, vous pourriez rencontrer des problèmes en raison de la nature de la version préliminaire.
Voici quelques problèmes courants et leurs solutions.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Conteneurs Linux**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Des erreurs de build se produisent lors du débogage d’une application de console ou web .NET Core  

Cela peut être lié au non-partage du lecteur sur lequel réside le projet avec Docker pour Windows.  Vous pourriez recevoir une erreur telle que la suivante :

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Pour résoudre ce problème :

1. Dans la zone de notification, cliquez avec le bouton droit sur **Docker pour Windows**, puis sélectionnez **Paramètres**.  
2. Sélectionnez **Lecteurs partagés**, puis partagez le lecteur où se trouve le projet.

### <a name="windows-containers"></a>**Conteneurs Windows**

Les problèmes suivants sont spécifiques au débogage des applications de console et web .NET Framework dans des conteneurs Windows.

#### <a name="prerequisites"></a>Composants requis

1. Visual Studio 2017 RC (ou version ultérieure) avec la charge de travail .NET Core et Docker (version préliminaire) doivent être installés.
2. Mise à jour anniversaire de Windows 10 avec les derniers correctifs de mise à jour Windows. En particulier [KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016) doit être installé. 
3. [Docker pour Windows](https://docs.docker.com/docker-for-windows/) (version 1.13.0 ou version ultérieure) doit être installé.
4. **Basculer vers les conteneurs Windows** doit être sélectionné. Dans la zone de notification, cliquez sur **Docker pour Windows**, puis sélectionnez **Basculer vers les conteneurs Windows**. Après le redémarrage de l’ordinateur, assurez-vous que ce paramètre est conservé.

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>La sortie de la console n’apparaît pas dans la fenêtre de sortie de Visual Studio pendant le débogage d’une application de console

Il s’agit d’un problème connu avec le débogueur Visual Studio (msvsmon.exe), qui n’est actuellement pas conçu pour ce scénario. La prise en charge de ce scénario peut être incluse dans une version ultérieure. Pour afficher la sortie de l’application de console dans Visual Studio, utilisez **Docker : Démarrer le projet**, qui équivaut à **Démarrer sans débogage**.

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>Le débogage d’applications web avec la configuration de version échoue avec l’erreur (403) Interdit

Pour contourner ce problème, ouvrez web.release.config dans la solution, puis placez en commentaire ou supprimez les lignes suivantes :

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Conteneurs Linux**

#### <a name="unable-to-validate-volume-mapping"></a>Impossible de valider le mappage de volume
Le mappage de volume est requis pour partager le code source et les fichiers binaires de votre application avec le dossier de l’application dans le conteneur.  Les mappages de volume spécifique sont contenus dans docker-compose.dev.debug.yml et docker-compose.dev.release.yml. Au fur et à mesure que les fichiers sont modifiés sur votre ordinateur hôte, les conteneurs reflètent ces modifications dans une structure de dossiers similaire.

Pour activer le mappage de volume :

1. Dans la zone de notification, cliquez sur **Moby**, puis sélectionnez **Paramètres**.
2. Sélectionnez **Lecteurs partagés**.
3. Sélectionnez le lecteur qui héberge votre projet et le lecteur où se trouve %USERPROFILE%.
4. Cliquez sur **Apply**.

Pour vérifier si le mappage de volume fonctionne, effectuez la reconstruction et sélectionnez F5 dans Visual Studio une fois qu’un ou plusieurs lecteurs ont été partagés, ou exécutez le code suivant à partir d’une invite de commandes.

> [!NOTE]
> Cet exemple suppose que le dossier Utilisateurs se trouve sur le lecteur C et qu’il a été partagé.
> Le cas échéant, procédez à une révision si vous avez partagé un autre lecteur.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

Exécutez le code suivant dans le conteneur Linux.

```
/ # ls
```

Vous devriez voir une liste de répertoires dans le dossier Users/Public. Si aucun fichier ne s’affiche et que votre dossier /c/Users/Public n’est pas vide, le mappage des volumes n’est pas configuré correctement.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Passez au répertoire wormhole pour voir le contenu du répertoire `/c/Users/Public` :

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> Lorsque vous travaillez avec des machines virtuelles Linux, le système de fichiers du conteneur est sensible à la casse.

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>Build : échec inattendu de la tâche PrepareForBuild

Microsoft.DotNet.Docker.CommandLine.ClientException : une erreur s’est produite lors de la tentative de connexion.

Vérifiez que l’hôte Docker par défaut est en cours d’exécution. Ouvrez une invite de commandes et exécutez :

```
docker info
```

Si elle renvoie une erreur, essayez de démarrer l’application de bureau **Docker pour Windows**. Si l’application de bureau est en cours d’exécution, alors **Moby** doit s’afficher dans la zone de notification. Cliquez avec le bouton droit sur **Moby**, puis ouvrez **Paramètres**. Cliquez sur **Réinitialiser**, puis redémarrez Docker.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Une boîte de dialogue d’erreur apparaît lorsque vous effectuez l’opération Ajouter -> Prise en charge ou débogage (F5) de Docker sur une application ASP.NET Core dans un conteneur

Après la désinstallation et l’installation des extensions, le cache MEF (Managed Extensibility Framework) de Visual Studio peut être endommagé. Dans ce cas, cela peut générer des messages d’erreur lors de l’ajout de la prise en charge et/ou de la tentative d’exécution ou du débogage (F5) de Docker sur votre application ASP.NET Core. Pour contourner provisoirement le problème, procédez comme suit pour supprimer et régénérer le cache MEF.

1. Fermez toutes les instances de Visual Studio.
1. Ouvrez %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\.
1. Supprimez les dossiers suivants :
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Ouvrez Visual Studio.
1. Réessayez le scénario.
