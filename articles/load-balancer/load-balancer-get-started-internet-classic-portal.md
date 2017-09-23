---
title: "Créer un équilibrage de charge accessible sur Internet à l’aide du Portail Azure Classic | Microsoft Docs"
description: "Découvrez comment créer un équilibreur de charge accessible sur Internet dans un modèle de déploiement classique à l’aide du portail Azure Classic."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: a022154f5eca6de2d2dbfc1b9aa30d2ea0a7d650
ms.contentlocale: fr-fr
ms.lasthandoff: 01/24/2017

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Création d’un équilibreur de charge accessible sur Internet (classique) dans le portail Azure Classic

> [!div class="op_single_selector"]
> * [Portail Azure Classic](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Interface de ligne de commande Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Services cloud Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Avant d’utiliser des ressources Azure, il est important de comprendre qu’Azure dispose actuellement de deux modèles de déploiement : Azure Resource Manager et classique. Veillez à bien comprendre les [modèles et outils de déploiement](../azure-classic-rm.md) avant d’utiliser une ressource Azure. Pour consulter la documentation relative aux différents outils, cliquez sur les onglets situés en haut de cet article. Cet article traite du modèle de déploiement classique. Vous pouvez également [découvrir comment créer un équilibreur de charge accessible sur Internet à l’aide d’Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configurer un équilibrage de charge accessible sur Internet pour les machines virtuelles

Afin d'équilibrer le trafic réseau à partir d'Internet sur les machines virtuelles d'un service cloud, vous devez créer un jeu d'équilibrage de la charge. Cette procédure suppose que vous avez déjà créé les machines virtuelles et qu’elles sont toutes dans le même service cloud.

**Pour configurer un ensemble d'équilibrage de charge interne pour les machines virtuelles**

1. Dans le portail Azure Classic, cliquez sur **Machines virtuelles**, puis sur le nom d’une machine virtuelle du jeu d’équilibrage de la charge.
2. Cliquez sur **Points de terminaison**, puis sur **Ajouter**.
3. Sur la page **Ajouter un point de terminaison à une machine virtuelle** , cliquez sur la flèche droite.
4. Dans la page **Spécifier les détails du point de terminaison** :

   * Dans **Nom**, saisissez le nom du point de terminaison ou sélectionnez-en un dans la liste des points de terminaison prédéfinis pour les protocoles communs.
   * Dans **Protocole**, sélectionnez le protocole requis, TCP ou UDP, pour ce type de point de terminaison.
   * Dans **Port public et Port privé**, entrez les numéros de port dont se servira la machine virtuelle. Vous pouvez utiliser le port privé et des règles de pare-feu sur la machine virtuelle pour rediriger le trafic de façon pertinente pour votre application. Le port privé et le port public peuvent être identiques. Par exemple, pour un point de terminaison pour le trafic Web (HTTP), vous pouvez attribuer le port 80 comme port public ou privé.

5. Sélectionnez **Créer un jeu d'équilibrage de la charge**, puis cliquez sur la flèche vers la droite.
6. Sur la page **Configurer le jeu d'équilibrage de la charge** , indiquez le nom du jeu d'équilibrage de charge et attribuez les valeurs correspondant au comportement de sonde de l'équilibrage de charge Azure. L'équilibrage de la charge utilise des sondes pour déterminer si les machines virtuelles du jeu d'équilibrage de la charge sont en mesure de recevoir le trafic entrant.
7. Cliquez sur la coche pour créer le point de terminaison à charge équilibrée. **Oui** s’affiche dans la colonne **Nom du jeu d’équilibrage de la charge** de la page **Points de terminaison** de la machine virtuelle.
8. Dans le portail, cliquez sur **Machines virtuelles**, sur le nom d’une machine virtuelle supplémentaire du jeu d’équilibrage de la charge, sur **Points de terminaison**, puis sur **Ajouter**.
9. Sur la page **Ajouter un point de terminaison à la machine virtuelle**, cliquez sur **Ajouter un point de terminaison à un jeu d’équilibrage de la charge existant**, sélectionnez le nom du jeu d’équilibrage de la charge, puis cliquez sur la flèche vers la droite.
10. Sur la page **Spécifier les détails du point de terminaison** , tapez le nom du point de terminaison, puis cliquez sur la coche.

Pour les machines virtuelles supplémentaires dans le jeu d’équilibrage de la charge, répétez les étapes 8 à 10.

## <a name="next-steps"></a>Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

