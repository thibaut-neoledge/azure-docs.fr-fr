---
title: "Vue d’ensemble d’Azure Cloud Shell (version préliminaire) | Microsoft Docs"
description: "Vue d’ensemble d’Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 44d2ac2fd35621ab8cd8d7584744139ee3bab5c2
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="overview-of-azure-cloud-shell-preview"></a>Vue d’ensemble d’Azure Cloud Shell (version préliminaire)
Azure Cloud Shell est un shell interactif, accessible par navigateur pour la gestion des ressources Azure.
Il vous donne la possibilité de choisir l’expérience d’interpréteur de commandes la plus adaptée à votre façon de travailler.
Les utilisateurs Linux peuvent choisir une expérience Bash, et les utilisateurs Windows l’option PowerShell.

![Bash dans Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell dans Cloud Shell](media/overview/overview-ps-pic.png)

## <a name="features"></a>Caractéristiques
### <a name="browser-based-shell-experience"></a>Expérience shell basée sur navigateur
Cloud Shell permet d’accéder à une expérience de ligne de commande basée sur navigateur avec les tâches de gestion Azure à l’esprit.
Exploitez Cloud Shell pour travailler librement à partir d’une machine locale d’une façon que seul le cloud peut fournir.

### <a name="choice-of-preferred-shell-experience"></a>Choix de votre expérience d’interpréteur de commandes préféré
Azure Cloud Shell vous donne la possibilité de choisir l’expérience d’interpréteur de commandes la plus adaptée à votre façon de travailler.
Les utilisateurs Linux peuvent choisir une expérience Bash, et les utilisateurs Windows l’option PowerShell.

### <a name="pre-configured-azure-workstation"></a>Station de travail Azure préconfigurée
Cloud Shell est préinstallé avec des outils de ligne de commande et la prise en charge de langages populaires afin de pouvoir travailler plus rapidement.

Affichez la liste complète des outils pour les expériences [Bash](features.md#tools) et [PowerShell.](features-powershell.md#tools)

### <a name="automatic-authentication"></a>Authentification automatique
Cloud Shell s’authentifie automatiquement de façon sécurisée sur chaque session pour accéder immédiatement à vos ressources via Azure CLI 2.0.

### <a name="connect-your-azure-file-storage"></a>Connexion à votre stockage de fichiers Azure
Les machines Cloud Shell sont temporaires et nécessitent ainsi qu’un partage de fichiers Azure soit monté en tant que `clouddrive` pour conserver votre répertoire $Home.
Lors du premier lancement, Cloud Shell vous invite à créer un groupe de ressources, un compte de stockage et un partage de fichiers en votre nom. Il s’agit d’une étape unique, et ces ressources sont automatiquement jointes pour toutes les sessions. Un partage de fichier unique peut être mappé et sera utilisé par Bash et PowerShell dans Cloud Shell.

#### <a name="create-new-storage"></a>Créer un stockage
![](media/overview/basic-storage.png)

Un compte de stockage localement redondant (LRS) et un partage de fichiers Azure peuvent être créés en votre nom. Le partage de fichiers Azure servira pour les environnements Bash et PowerShell si vous choisissez d’utiliser les deux. Les coûts de stockage standard s’appliquent.

Trois ressources sont créées en votre nom :
1. Groupe de ressources nommé : `cloud-shell-storage-<region>`
2. Compte de stockage nommé : `cs<uniqueGuid>`
3. Partage de fichiers nommé : `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash dans Cloud Shell crée également une image de disque de 5 Go par défaut pour rendre `$Home` persistant. Tous les fichiers figurant dans votre répertoire $Home, tels que les clés SSH, sont conservés dans l’image de disque utilisateur stockée dans votre partage de fichiers monté. Appliquez les meilleures pratiques lors de l’enregistrement des fichiers dans votre répertoire $Home et le partage de fichiers monté.

#### <a name="use-existing-resources"></a>Utiliser les ressources existantes
![](media/overview/advanced-storage.png)

Vous disposez d’une option avancée qui permet d’associer des ressources existantes à Cloud Shell.
À l’invite de configuration du stockage, cliquez sur « Afficher les paramètres avancés » pour afficher des options supplémentaires.
Les listes déroulantes sont filtrées pour la région Cloud Shell qui vous a été attribuée et pour les comptes de stockage localement/globalement redondant.

[Découvrez le stockage Cloud Shell, la mise à jour des partages de fichiers et le chargement/téléchargement de fichiers.](persisting-shell-storage.md)

## <a name="concepts"></a>Concepts
* Cloud Shell s’exécute sur une machine temporaire fournie par session et par utilisateur
* Cloud Shell expire après 20 minutes sans activité interactive
* Cloud Shell est accessible uniquement avec un partage de fichiers attaché
* Cloud Shell utilise le même partage de fichiers pour Bash et PowerShell
* Cloud Shell est affecté à une machine par compte d’utilisateur
* Les autorisations sont définies en tant qu’utilisateur Linux standard (Bash)

En savoir plus sur les fonctionnalités de [Bash dans Cloud Shell](features.md) et [PowerShell dans Cloud Shell](features-powershell.md).

## <a name="examples"></a>Exemples
* Utiliser des scripts pour automatiser les tâches de gestion Azure
* Gérer simultanément des ressources Azure via le portail Azure et des outils à ligne de commande Azure
* Essayer Azure CLI 2.0 ou les applets de commande Azure PowerShell

Essayez ces exemples dans les démarrages rapides pour [Bash dans Cloud Shell](quickstart.md) et [PowerShell dans Cloud Shell](quickstart-powershell.md).

## <a name="pricing"></a>Tarification
La machine qui héberge Cloud Shell est gratuite, avec comme condition préalable le montage d’un partage de fichiers Azure. Les coûts de stockage standard s’appliquent.

## <a name="supported-browsers"></a>Navigateurs pris en charge
Cloud Shell est recommandé pour Chrome, Safari et Edge.
Si Cloud Shell est pris en charge par Chrome, Firefox, Safari, IE et Edge, il est soumis aux paramètres propres au navigateur.
