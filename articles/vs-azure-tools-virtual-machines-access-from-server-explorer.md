---
title: "Accès aux machines virtuelles Azure à partir de l’Explorateur de serveurs | Microsoft Docs"
description: "Obtenez une présentation de l’affichage, de la création et de la gestion des machines virtuelles Azure dans l’Explorateur de serveurs de Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: kraigb
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 009b325054737b63d5bdaefbe005d6a613126f52
ms.contentlocale: fr-fr
ms.lasthandoff: 11/17/2016

---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Accès aux machines virtuelles Azure à partir de l’Explorateur de serveurs
Grâce à l’Explorateur de serveurs dans Visual Studio, vous pouvez afficher des informations concernant vos machines virtuelles hébergées par Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>Accès aux machines virtuelles dans l’Explorateur de serveurs
Si vous avez des machines virtuelles hébergées par Azure, vous pouvez y accéder depuis l’Explorateur de serveurs. Vous devez d’abord vous connecter à votre abonnement Azure pour afficher vos services mobiles. Pour vous connecter, ouvrez le menu contextuel du nœud Azure dans l’Explorateur de serveurs, puis choisissez **Se connecter à Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>Pour obtenir des informations sur vos machines virtuelles
1. Dans l’Explorateur de serveurs, choisissez une machine virtuelle, puis appuyez sur la touche F4 pour afficher sa fenêtre de propriétés.
   
    Le tableau suivant indique les propriétés disponibles. Toutes les propriétés sont en lecture seule. Utilisez le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885) pour les modifier.
   
   | Propriété | Description |
   | --- | --- |
   | Nom DNS |URL comportant l’adresse Internet de la machine virtuelle. |
   | Environnement |Pour une machine virtuelle, la valeur de cette propriété est toujours Production. |
   | Name |Nom de la machine virtuelle. |
   | Taille |Taille de la machine virtuelle, qui reflète la quantité de mémoire et d’espace disque disponibles. Pour plus d’informations, consultez « Configurer les tailles pour les services cloud ». |
   | État |Les valeurs incluent : Démarrage en cours, Démarré, En cours d’arrêt, Arrêté et Extraction de l’état. Si Extraction de l’état s’affiche, l’état actuel est inconnu. Les valeurs de cette propriété ne sont pas les mêmes que celles qui sont utilisées dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885). |
   | SubscriptionID |ID d’abonnement de votre compte Azure. Vous pouvez obtenir cette information sur le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885) en affichant les propriétés de l’abonnement. |
2. Sélectionnez un nœud de point de terminaison, puis ouvrez la fenêtre **Propriétés** .
3. Le tableau suivant décrit les propriétés des points de terminaison disponibles. Toutes ces propriétés sont en lecture seule. Pour ajouter ou modifier les points de terminaison d’une machine virtuelle, utilisez le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885). 
   
   | Propriété | Description |
   | --- | --- |
   | Name |Identificateur du point de terminaison. |
   | Port privé |Port d’accès réseau interne à votre application. |
   | Protocole |Protocole utilisé par la couche de transport du point de terminaison (TCP ou UDP). |
   | Port public |Port utilisé pour l’accès public à votre application. |

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’utilisation des rôles Azure dans Visual Studio, consultez [Utilisation du Bureau à distance avec des rôles Azure](vs-azure-tools-remote-desktop-roles.md).


