
---
title: Présentation de l’équilibrage de charge accessible sur Internet | Microsoft Docs
description: Présentation de l’équilibrage de charge accessible sur Internet et ses fonctionnalités. Fonctionnement de l’équilibrage de charge pour Azure avec des machines virtuelles et des services cloud.
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2016
ms.author: sewhee

---
# Présentation de l’équilibrage de charge accessible sur Internet
L’équilibreur de charge Azure mappe l’adresse IP publique et le numéro de port du trafic entrant à l’adresse IP privée et au numéro de port de la machine virtuelle (et inversement) pour le trafic de réponse de la machine virtuelle. Les règles d’équilibrage de charge vous permettent de distribuer des types spécifiques de trafic entre plusieurs machines virtuelles ou services. Par exemple, vous pouvez répartir la charge du trafic des requêtes Web sur plusieurs serveurs ou rôles Web.

> [!NOTE]
> Azure Load Balancer fournit un trafic de réseau de distribution par hachage sur plusieurs instances de machines virtuelles suivant les paramètres par défaut (pour en savoir plus sur la distribution par hachage, consultez les [fonctionnalités de l’équilibreur de charge](load-balancer-overview.md#load-balancer-features) . Si vous cherchez une affinité de session, consultez la rubrique [Mode de distribution de l’équilibreur de charge](load-balancer-distribution-mode.md).
> 
> 

Pour un service cloud contenant des instances de rôles Web ou de rôles de travail, vous pouvez définir un point de terminaison public dans le fichier de définition de service (.csdef).

Le fichier servicedefinition.csdef contient la configuration de point de terminaison et lorsque vous avez plusieurs instances de rôle pour un déploiement de rôle Web ou de travail, l’équilibrage de charge sera configuré pour celui-ci. Pour ajouter des instances à votre déploiement de cloud, vous devez modifier le nombre d’instances dans le fichier de configuration de service (.csfg).

La figure suivante présente un point de terminaison à charge équilibrée pour le trafic Web chiffré partagé entre trois machines virtuelles pour les ports public et privé 443. Celles-ci sont incluses dans un jeu d’équilibrage de la charge.

![exemple d’équilibrage de charge public](./media/load-balancer-internet-overview/IC727496.png))

Lorsque les clients Internet envoient des demandes de page Web à l’adresse IP publique du service cloud sur le port TCP 443, Azure Load Balancer distribue les demandes entre les trois machines virtuelles du jeu à charge équilibrée. Des informations supplémentaires sur l’algorithme de l’équilibreur de charge sont disponibles sur la [page de présentation de l’équilibreur de charge](load-balancer-overview.md#load-balancer-features).

## Étapes suivantes
Après vous être familiarisé avec les équilibreurs de charge avec accès par Internet, vous pouvez également consulter des informations sur [l’équilibreur de charge interne](load-balancer-internal-overview.md) pour mieux comprendre quel équilibreur de charge convient le mieux à votre déploiement de cloud.

Vous pouvez également [commencer par créer un équilibreur de charge avec accès par Internet](load-balancer-get-started-internet-arm-ps.md) et configurer le type de [mode de distribution](load-balancer-distribution-mode.md) pour un comportement spécifique de trafic réseau d’équilibrage de charge.

Si votre application doit maintenir des connexions actives pour les serveurs situés derrière un équilibreur de charge, vous pouvez obtenir plus d’informations sur les [paramètres de délai d'expiration TCP pour un équilibrage de charge](load-balancer-tcp-idle-timeout.md). Ainsi, vous en saurez plus sur le comportement d’une connexion inactive lorsque vous utilisez l'équilibreur de charge Azure.

<!---HONumber=AcomDC_0831_2016-->