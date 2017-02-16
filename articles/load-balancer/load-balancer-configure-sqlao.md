---
title: "Configuration de l&quot;équilibrage de charge pour SQL Always On | Microsoft Docs"
description: "Configuration de l’équilibreur de charge de manière à ce qu’il fonctionne avec SQL Always On et procédure d’exploitation de Powershell pour créer l’équilibreur de charge pour l’implémentation de SQL"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: 68aad6253f185d53fdd7f11c8660c7287ef12655

---

# <a name="configure-load-balancer-for-sql-always-on"></a>Configuration de l'équilibrage de charge pour SQL Always On

Les groupes de disponibilité Always On de SQL Server peuvent maintenant être exécutés avec l’équilibrage de charge interne (ILB). Le groupe de disponibilité constitue la solution phare de SQL Server pour une haute disponibilité et la récupération d’urgence. L’écouteur du groupe de disponibilité permet aux applications clientes de se connecter en toute transparence au réplica principal, quel que soit le nombre de réplicas dans la configuration.

Le nom (DNS) de l'écouteur est mappé vers une adresse IP de l’équilibrage de charge et l’équilibrage de charge Azure dirige le trafic entrant vers le serveur principal du jeu de réplicas uniquement.

Vous pouvez utiliser le support de l'équilibrage de charge pour les points de terminaison (écouteur) de SQL Server AlwaysOn. Vous avez désormais un contrôle sur l'accessibilité de l'écouteur et vous pouvez choisir l'adresse IP de l’équilibrage de charge à partir d'un sous-réseau spécifique dans votre réseau virtuel.

À l'aide de l'ILB sur l'écouteur, le point de terminaison du serveur SQL (par exemple, Server=tcp:ListenerName,1433;Database=DatabaseName) est accessible uniquement via :

* Services et machines virtuelles dans le même réseau virtuel
* Services et machines virtuelles d’un réseau local connecté
* Services et machines virtuelles de réseaux virtuels interconnectés

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Figure 1 : SQL AlwaysOn configuré avec équilibrage de charge côté Internet

## <a name="add-internal-load-balancer-to-the-service"></a>Ajout d’un équilibrage de charge interne au service

1. Dans l’exemple suivant, nous allons configurer un réseau virtuel contenant un sous-réseau nommé 'Sous-réseau 1' :

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Ajout des points de terminaison avec une charge équilibrée pour l'ILB sur chaque machine virtuelle

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    Dans l'exemple ci-dessus, la machine virtuelle 2 est appelée « sqlsvc1 » et « sqlsvc2 » en cours d'exécution dans le service de cloud « Sqlsvc ». Après avoir créé l’ILB avec le commutateur `DirectServerReturn`, vous ajoutez à l’ILB des points de terminaison à charge équilibrée pour permettre à SQL de configurer les écouteurs pour les groupes de disponibilité.

Pour plus d’informations sur SQL AlwaysOn, voir [Configurer un équilibrage de charge interne pour un groupe de disponibilité AlwaysOn dans Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Voir aussi
[Prise en main de la configuration d’un équilibrage de charge sur Internet](load-balancer-get-started-internet-arm-ps.md)

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configuration d’un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Jan17_HO2-->


