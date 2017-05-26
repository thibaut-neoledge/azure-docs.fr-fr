---
title: "Utilisation de la fenêtre Azure Cloud Shell (version préliminaire) | Microsoft Docs"
description: "Description de la fenêtre Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 69316d271fc4847565d183293013580b61695731
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>Utilisation de la fenêtre Azure Cloud Shell

Ce document explique comment utiliser la fenêtre Azure Cloud Shell.

## <a name="concurrent-sessions"></a>Sessions simultanées
Cloud Shell permet plusieurs sessions simultanées dans les onglets du navigateur en permettant à chaque session d’exister sous la forme d’un processus Bash distinct.
Lorsque vous quittez une session, veillez à fermer chaque fenêtre de session dans la mesure où chaque processus s’exécute indépendamment sur la même machine.

## <a name="restart-cloud-shell"></a>Redémarrer Cloud Shell
![](media/recycle.png)
* Cliquez sur l’icône de redémarrage de la barre d’outils pour réinitialiser la machine Cloud Shell.

> ![AVERTISSEMENT] Le redémarrage de Cloud Shell réinitialise l’état de la machine et tous les fichiers non conservés par clouddrive sont perdus.

## <a name="minimize--maximize-cloud-shell-window"></a>Réduire et agrandir la fenêtre Cloud Shell
![](media/minmax.png)
* Cliquez sur l’icône de réduction sur la partie supérieure droite de la fenêtre pour la masquer. Cliquez de nouveau sur l’icône Cloud Shell pour la réafficher.
* Cliquez sur l’icône d’agrandissement pour agrandir la fenêtre à sa hauteur maximale. Pour revenir à la taille de fenêtre précédente, cliquez sur Restaurer.

## <a name="copy-and-paste"></a>Copier et coller
* Windows : `Ctrl-insert` et `Shift-insert` ou cliquez avec le bouton droit sur le menu déroulant pour copier/coller
  * FireFox/IE peuvent ne pas prendre en charge correctement les autorisations de Presse-papiers
* Mac OS : `Cmd-c` et `Cmd-v` ou cliquez avec le bouton droit sur le menu déroulant pour copier/coller

## <a name="resize-cloud-shell-window"></a>Redimensionner la fenêtre Cloud Shell
* Cliquez et faites glisser le bord supérieur de la barre d’outils vers haut ou le bas pour redimensionner la fenêtre Cloud Shell.

## <a name="scrolling-text-display"></a>Défilement du texte
* Faites défiler le texte avec la souris ou un pavé tactile

## <a name="exit-command"></a>Commande Quitter
Cliquez sur `exit` pour mettre un terme à la session active. Ce comportement se produit par défaut au bout de 10 minutes en l’absence d’interaction.

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Cloud Shell](quickstart.md)  
