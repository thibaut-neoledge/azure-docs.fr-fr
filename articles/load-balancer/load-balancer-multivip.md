---
title: Plusieurs adresses IP virtuelles par service cloud
description: "Vue d’ensemble de multiVIP et définition de plusieurs adresses IP virtuelles sur un service cloud"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 85f6d26a-3df5-4b8e-96a1-92b2793b5284
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: f40e0501eed8d5f296e7c79d8a35705a695ae6fd
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---

# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configuration de plusieurs adresses IP virtuelles pour un service cloud

Vous pouvez accéder aux services cloud Azure via Internet à l’aide d’une adresse IP fournie par Azure. Cette adresse IP publique est appelée adresse IP virtuelle, car elle est liée à l’équilibrage de charge Azure et non aux instances de la machine virtuelle (VM) dans le service cloud. Vous pouvez accéder à une instance de machine virtuelle dans un service cloud à l’aide d’une adresse IP virtuelle unique.

Toutefois, il existe des scénarios dans lesquels vous pouvez avoir besoin de plusieurs adresses IP virtuelles comme point d’entrée vers le même service cloud. Par exemple, votre service cloud peut héberger plusieurs sites web qui exigent une connexion SSL à l’aide du port 443 par défaut, chaque site étant hébergé pour un autre client ou locataire. Dans ce scénario, vous devez avoir une adresse IP publique différente pour chaque site Web. Le diagramme suivant illustre un hébergement Web mutualisé typique nécessitant plusieurs certificats SSL sur le même port public.

![Scénario avec plusieurs adresses IP virtuelles SSL](./media/load-balancer-multivip/Figure1.png)

Dans l’exemple ci-dessus, toutes les adresses IP virtuelles utilisent le même port public (443) et le trafic est redirigé vers une ou plusieurs machines virtuelles à équilibrage de charge sur un seul port privé de l’adresse IP interne du service cloud qui héberge les sites Web.

> [!NOTE]
> L’hébergement de plusieurs écouteurs de groupe de disponibilité SQL AlwaysOn dans le même jeu de machines virtuelles nécessite également l’utilisation de plusieurs adresses IP virtuelles.

Les adresses IP virtuelles sont dynamiques par défaut, ce qui signifie que l’adresse IP réelle affectée au service cloud peut changer au fil du temps. Pour éviter cela, vous pouvez réserver une adresse IP virtuelle pour votre service. Pour plus d’informations sur les adresses IP virtuelles réservées, consultez [Adresses IP publiques réservées](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Consultez [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses/) pour plus d’informations sur la tarification des adresses IP virtuelles et réservées.

Vous pouvez utiliser PowerShell pour vérifier les adresses IP virtuelles utilisées par vos services cloud, ainsi qu’ajouter et supprimer des adresses IP virtuelles, associer une adresse IP virtuelle à un point de terminaison et configurer l’équilibrage de charge sur une adresse IP virtuelle spécifique.

## <a name="limitations"></a>Limitations

À ce stade, la fonctionnalité d'adresses IP virtuelles multiples est limitée aux scénarios suivants :

* **IaaS uniquement**. Vous ne pouvez activer les adresses IP virtuelles multiples que pour les services cloud qui contiennent des machines virtuelles. Vous ne pouvez pas utiliser les adresses IP virtuelles multiples dans les scénarios PaaS avec des instances de rôles.
* **PowerShell uniquement**. Vous pouvez gérer les adresses IP virtuelles multiples uniquement à l'aide de PowerShell.

Ces limitations sont temporaires et peuvent changer à tout moment. N'oubliez pas de revenir sur cette page pour consulter les modifications.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Ajout d’une adresse IP virtuelle à un service cloud
Pour ajouter une adresse IP virtuelle à votre service, exécutez la commande PowerShell suivante :

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Cette commande affiche un résultat semblable à l’exemple suivant :

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Suppression d’une adresse IP virtuelle d’un service cloud
Pour supprimer l’adresse IP virtuelle ajoutée à votre service dans l’exemple ci-dessus, exécutez la commande PowerShell suivante :

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Vous ne pouvez supprimer une adresse IP virtuelle que si aucun point de terminaison ne lui est associé.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Récupération des informations de l’adresse IP virtuelle à partir d’un service cloud
Pour récupérer les adresses IP virtuelles associées à un service cloud, exécutez le script PowerShell suivant :

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Le script affiche un résultat semblable à l’exemple suivant :

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

Dans cet exemple, le service cloud a trois adresses IP virtuelles (Vip) :

* **Vip1** est l’adresse IP virtuelle par défaut, comme en atteste la valeur de IsDnsProgrammedName définie sur true.
* **Vip2** et **Vip3** ne sont pas utilisées étant donné qu’elles n’ont pas d’adresses IP. Elles servent uniquement si vous associez un point de terminaison à l’adresse IP virtuelle.

> [!NOTE]
> Votre abonnement est uniquement facturé pour les adresses IP virtuelles supplémentaires après leur association à un point de terminaison. Pour plus d’informations sur la tarification, voir la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Association d’une adresse IP virtuelle à un point de terminaison

Pour associer une adresse IP virtuelle sur un service cloud à un point de terminaison, exécutez la commande PowerShell suivante :

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

La commande crée un point de terminaison lié à l’adresse IP virtuelle appelée *Vip2* sur le port *80*, ainsi que des liens vers la machine virtuelle nommée *myVM1* dans un service cloud nommé *myService* à l’aide de *TCP* sur le port *8080*.

Pour vérifier la configuration, exécutez la commande PowerShell suivante :

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Le résultat ressemble à l’exemple qui suit :

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Activation de l’équilibrage de charge sur une adresse IP virtuelle spécifique

Vous pouvez associer une adresse IP virtuelle unique à plusieurs machines virtuelles à des fins d’équilibrage de charge. Par exemple, supposons que vous avez un service cloud nommé *myService*, et deux machines virtuelles nommées *myVM1* et *myVM2*. Votre service cloud dispose de plusieurs adresses IP virtuelles, dont une nommée *Vip2*. Si vous souhaitez vous assurer que l’ensemble du trafic vers le port *81* sur *Vip2* est équilibré entre *myVM1* et *myVM2* sur le port *8181*, exécutez le script PowerShell suivant :

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Vous pouvez également mettre à jour votre équilibrage de charge pour utiliser une adresse IP virtuelle différente. Par exemple, si vous exécutez la commande PowerShell suivante, vous modifiez le jeu d’équilibrage de la charge, afin qu’il utilise une adresse IP virtuelle nommée Vip1 :

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Étapes suivantes

[Analyse des journaux d’Azure Load Balancer](load-balancer-monitor-log.md)

[Présentation de l’équilibrage de charge accessible sur Internet](load-balancer-internet-overview.md)

[Prise en main de l’équilibrage de charge accessible sur Internet](load-balancer-get-started-internet-arm-ps.md)

[Présentation du réseau virtuel.](../virtual-network/virtual-networks-overview.md)

[API REST d’adresse IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx)

