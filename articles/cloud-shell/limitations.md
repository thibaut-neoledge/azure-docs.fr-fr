---
title: "Limitations d’Azure Cloud Shell (version préliminaire) | Microsoft Docs"
description: "Vue d’ensemble des limites d’Azure Cloud Shell"
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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a48b32d3e2383497f68ab4eba81af81dd9f8449d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="limitations"></a>Limitations
Azure Cloud Shell a les limitations connues suivantes.

## <a name="system-state-and-persistence"></a>État du système et persistance
La machine fournissant votre session Cloud Shell est temporaire et est recyclée une fois votre session inactive pendant 10 minutes. Cloud Shell requiert qu’un partage de fichiers soit monté.
* Avec le stockage monté, seules les modifications apportées à votre répertoire `$Home` ou `clouddrive` sont conservées
  * Les partages de fichiers peuvent être montés uniquement depuis votre [région affectée](persisting-shell-storage.md#pre-requisites-for-manual-mounting)
  * Azure Files prend uniquement en charge les comptes de stockage LRS et GRS

## <a name="user-permissions"></a>Autorisations utilisateur
Les autorisations sont définies en tant qu’utilisateurs standards sans accès sudo. Toute installation en dehors de votre $Home n’est pas conservée.
Certaines commandes, par exemple `git clone` dans le répertoire `clouddrive`, n’ont pas les autorisations appropriées, mais votre répertoire $Home si.

## <a name="browser-support"></a>Prise en charge des navigateurs
Cloud Shell prend en charge les dernières versions de Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox et Apple Safari. Safari en mode privé n’est pas pris en charge.

## <a name="copy-and-paste"></a>Copier et coller
Ctrl + v et Ctrl + c ne fonctionnent pas pour le copier/coller sur les machines Windows, veuillez utiliser Ctrl + Ins et MAJ + Ins pour le copier/coller.
Les options de copier/coller du bouton droit sont également disponibles, mais sont soumises à l’accès au presse-papiers du navigateur.

## <a name="usage-limits"></a>Limites d’utilisation
Cloud Shell est destiné aux cas d’utilisation interactifs, ainsi les sessions non interactives longues se terminent sans avertissement.

## <a name="network-connectivity"></a>Connectivité réseau
La latence dans Cloud Shell est soumise à la connectivité Internet locale, Cloud Shell continue d’essayer de fonctionner avec les instructions envoyées.

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Cloud Shell](quickstart.md)
