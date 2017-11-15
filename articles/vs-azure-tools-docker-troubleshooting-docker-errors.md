---
title: "Dépannage des erreurs du client Docker sur Windows avec Visual Studio | Microsoft Docs"
description: "Résolvez les problèmes d’utilisation de Visual Studio pour créer et déployer des applications web dans Docker sur Windows avec Visual Studio 2017."
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: 90dd5df4a607568e2f3a60791da2948af7ce4e50
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2017
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Résoudre des problèmes de développement Visual Studio 2017 avec Docker

Quand vous utilisez Visual Studio Tools pour Docker, vous pouvez rencontrer des problèmes au moment de la génération ou du débogage de votre application. Voici certains problèmes courants et les étapes de dépannage correspondantes.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Le partage de volume n’est pas activé. Activez le volume de partage dans les paramètres de Docker CE pour Windows (uniquement pour les conteneurs Linux)

Pour résoudre ce problème :

1. Dans la zone de notification, cliquez avec le bouton droit sur **Docker pour Windows**, puis sélectionnez **Paramètres**.
1. Sélectionnez **Lecteurs partagés**, puis partagez le lecteur système et le lecteur où se trouve le projet.

> [!NOTE]
> Si les fichiers sont affichés comme partagés, cliquez sur le lien « Réinitialiser les informations d’identification... » en bas de la boîte de dialogue pour réactiver le partage de volume.

![lecteurs partagés](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="unable-to-start-debugging"></a>Impossible de démarrer le débogage

L’une des raisons peut être liée à la présence de composants de débogage obsolètes dans votre dossier de profil utilisateur. Exécutez les commandes suivantes pour supprimer ces dossiers. Les composants de débogage les plus récents seront téléchargés lors de la session de débogage suivante.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Erreurs de réseau lors du débogage de votre application

Essayez d’exécuter le script téléchargeable à partir de [Cleanup Container Host Networking](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking). Ce script met à jour les composants réseau sur votre machine hôte.


## <a name="microsoftdockertools-github-repo"></a>Référentiel GitHub Microsoft/DockerTools

Pour tout autre problème que vous rencontrez, consultez les problèmes répertoriés dans [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues).