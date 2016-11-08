
---
title: Migrer des données utilisateur à partir d’Azure RemoteApp | Microsoft Docs
description: Apprenez à migrer vos données utilisateur dans et hors d’Azure RemoteApp.
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Migration de données dans et hors d’Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Vous pouvez utiliser de nombreux outils et méthodes différents pour transférer des [données utilisateur](remoteapp-upd.md) dans et hors d’Azure RemoteApp. Voici quelques méthodes :

* Copier et coller en utilisant le partage du Presse-papiers
* Copier des fichiers et des données vers un serveur de fichiers
* Copier des fichiers dans OneDrive Entreprise à l’aide d’un navigateur
* Copier des fichiers à l’aide de la redirection

> [!NOTE]
> Vous ne pouvez pas activer les agents de synchronisation OneDrive Entreprise ou Consumer, ils [ne sont pas pris en charge](remoteapp-onedrive.md) dans Azure RemoteApp.
> 
> 

## Utiliser le copier-coller dans l’Explorateur de fichiers
Le copier-coller à l’aide du Presse-papiers est activé dans les déploiements RemoteApp [par défaut](remoteapp-redirection.md). Cela permet aux utilisateurs de copier des fichiers entre leur ordinateur local et les applications RemoteApp. Souvent, lors de l’utilisation normale d’applications dans RemoteApp, les utilisateurs ont enregistré fichiers dans leur UPD. Le déplacement des données hors de RemoteApp est aisé :

1. [Publiez l’Explorateur de fichiers en tant qu’application](remoteapp-publish.md) dans une collection RemoteApp. (Notez qu’il s’agit d’une tâche administrative.)
2. Dites à vos utilisateurs de lancer l’application Explorateur de fichiers que vous avez publiée et de l’utiliser pour copier et coller des fichiers à la fois dans leur UPD et hors de celui-ci.

## Charger des fichiers et des données vers un serveur de fichiers à l’aide de la copie de fichiers réseau standard
Souvent, les organisations utilisent des serveurs de fichiers pour stocker des données générales. Si vous connaissez le nom du serveur ou son emplacement, vos utilisateurs peuvent parcourir le réseau local pour rechercher le serveur, puis copier leurs fichiers à cet emplacement, un peu comme la méthode précédente. Vous devez à nouveau publier l’Explorateur de fichiers sur RemoteApp et le partager avec vos utilisateurs.

> [!NOTE]
> Le serveur de fichiers doit être sur le réseau routable où RemoteApp a été déployé.
> 
> 

## Copier des fichiers vers OneDrive Entreprise
Bien que vous ne puissiez pas activer l’agent de synchronisation OneDrive Entreprise dans RemoteApp, vous pouvez toujours copier des fichiers à partir de votre UPD sur OneDrive Entreprise à l’aide d’un navigateur.

1. Publiez l’Explorateur de fichiers sur RemoteApp et demandez aux utilisateurs d’accéder aux fichiers à l’aide de cette application.
2. Il est plus facile de transférer des fichiers s’ils sont compressés, donc les utilisateurs doivent créer un fichier .zip qui contient tous les fichiers à déplacer vers OneDrive Entreprise.
3. Demandez aux utilisateurs d’accéder au portail Office 365, puis d’accéder à OneDrive et de charger le fichier .zip.

## Copier des fichiers à l’aide de la redirection de lecteur
Si vous avez activé [la redirection de lecteur](remoteapp-redirection.md), vous avez déjà créé un lecteur mappé pour vos utilisateurs. Dans ce cas, ils peuvent créer un fichier .zip contenant leurs fichiers sur le lecteur redirigé, puis les enregistrer sur leur ordinateur local.

<!---HONumber=AcomDC_0817_2016-->