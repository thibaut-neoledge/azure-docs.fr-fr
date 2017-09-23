---
title: "Déplacer une machine virtuelle (Classic) ou une instance de rôle Services cloud vers un autre sous-réseau - Azure PowerShell | Microsoft Docs"
description: "Découvrez comment déplacer des machines virtuelles (Classic) et des instances de rôle Services cloud vers un autre sous-réseau à l’aide de PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: b094f8338394ef2e84cad3070936d715411326a4
ms.contentlocale: fr-fr
ms.lasthandoff: 02/28/2017

---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Déplacer une machine virtuelle (Classic) ou une instance de rôle Services cloud vers un autre sous-réseau à l’aide de PowerShell
Vous pouvez utiliser PowerShell pour déplacer vos machines virtuelles (Classic) d’un sous-réseau à un autre dans le même réseau virtuel. Les instances de rôle peuvent être déplacées en modifiant le fichier CSCFG au lieu d’utiliser PowerShell.

> [!NOTE]
> Cet article explique comment déplacer des machines virtuelles déployées via le modèle de déploiement Classic uniquement.
> 
> 

Pourquoi déplacer des ordinateurs virtuels vers un autre sous-réseau ? La migration entre sous-réseaux est utile quand le sous-réseau plus ancien est trop petit et ne peut pas être étendu en raison des machines virtuelles existantes exécutées dans ce sous-réseau. Dans ce cas, vous pouvez créer un sous-réseau plus grand et migrer les machines virtuelles vers le nouveau sous-réseau, puis, une fois la migration terminée, vous pouvez supprimer l’ancien sous-réseau vide.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Comment déplacer une machine virtuelle vers un autre sous-réseau
Pour déplacer une machine virtuelle, exécutez l’applet de commande PowerShell Set-AzureSubnet, en prenant exemple sur le modèle ci-dessous. Dans l’exemple ci-dessous, nous déplaçons TestVM de son sous-réseau actuel vers Subnet-2. Assurez-vous de modifier l’exemple pour refléter votre environnement. Notez que chaque fois que vous exécutez l’applet de commande Update-AzureVM dans une procédure, elle redémarre votre machine virtuelle dans le cadre du processus de mise à jour.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Si vous avez spécifié une adresse IP privée interne pour votre machine virtuelle, vous devrez effacer ce paramètre avant de déplacer la machine virtuelle vers un nouveau sous-réseau. Dans ce cas, utilisez ce qui suit :

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Pour déplacer une instance de rôle vers un autre sous-réseau
Pour déplacer une instance de rôle, modifiez le fichier CSCFG. Dans l’exemple ci-dessous, nous déplaçons « Role0 » dans le réseau virtuel *VNETName* de son sous-réseau actuel vers *Subnet-2*. Étant donné que l’instance de rôle a déjà été déployée, vous devez juste modifier le nom du sous-réseau = Subnet-2. Assurez-vous de modifier l’exemple pour refléter votre environnement.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 

