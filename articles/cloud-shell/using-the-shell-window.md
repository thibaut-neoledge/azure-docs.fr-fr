---
title: "Utilisation de la fenêtre Azure Cloud Shell (version préliminaire) | Microsoft Docs"
description: "Description de la fenêtre Azure Cloud Shell."
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
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: a47961dfdaf178a6b793bd68105d9792a9275bb3
ms.contentlocale: fr-fr
ms.lasthandoff: 08/17/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>Utilisation de la fenêtre Azure Cloud Shell

Ce document explique comment utiliser la fenêtre Cloud Shell.

## <a name="concurrent-sessions"></a>Sessions simultanées
Cloud Shell permet plusieurs sessions simultanées dans les onglets du navigateur en permettant à chaque session d’exister sous la forme d’un processus Bash distinct.
Lorsque vous quittez une session, veillez à fermer chaque fenêtre de session dans la mesure où chaque processus s’exécute indépendamment sur la même machine.

## <a name="restart-cloud-shell"></a>Redémarrer Cloud Shell
![](media/recycle.png)
> [!WARNING]
> Le redémarrage de Cloud Shell réinitialise l’état de la machine et tous les fichiers non conservés par votre partage de fichiers sont perdus.

* Cliquez sur l’icône de redémarrage de la barre d’outils pour initialiser un nouvel environnement Cloud Shell.

## <a name="minimize--maximize-cloud-shell-window"></a>Réduire et agrandir la fenêtre Cloud Shell
![](media/minmax.png)
* Cliquez sur l’icône de réduction sur la partie supérieure droite de la fenêtre pour la masquer. Cliquez de nouveau sur l’icône Cloud Shell pour la réafficher.
* Cliquez sur l’icône d’agrandissement pour agrandir la fenêtre à sa hauteur maximale. Pour revenir à la taille de fenêtre précédente, cliquez sur Restaurer.

## <a name="copy-and-paste"></a>Copier et coller
* Windows : `Ctrl-insert` pour copier et `Shift-insert` pour coller. Le menu déroulant accessible par un clic droit permet également de copier/coller.
  * FireFox/IE peuvent ne pas prendre en charge correctement les autorisations de Presse-papiers.
* Mac OS : `Cmd-c` pour copier et `Cmd-v` pour coller. Le menu déroulant accessible par un clic droit permet également de copier/coller.

## <a name="resize-cloud-shell-window"></a>Redimensionner la fenêtre Cloud Shell
* Cliquez et faites glisser le bord supérieur de la barre d’outils vers haut ou le bas pour redimensionner la fenêtre Cloud Shell.

## <a name="scrolling-text-display"></a>Défilement du texte
* Faites défiler le texte du terminal avec la souris ou le pavé tactile.

## <a name="exit-command"></a>Commande Quitter
Cliquez sur `exit` pour mettre un terme à la session active. Ce comportement se produit par défaut au bout de 20 minutes en l’absence d’interaction.

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Cloud Shell](quickstart.md)

