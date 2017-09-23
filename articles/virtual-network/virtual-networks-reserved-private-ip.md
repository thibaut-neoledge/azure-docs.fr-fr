---
title: "Adresse IP privée interne statique - Machine virtuelle Azure - Classic"
description: Fonctionnement et gestion des adresses IP internes statiques (adresses IP dynamiques)
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: cf9ee59ca4e44ed01836c2efb1f4df5f073bf6e0
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017

---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Comment définir une adresse IP privée interne statique à l’aide de PowerShell (Classic)
Dans la plupart des cas, il n’est pas nécessaire de spécifier une adresse IP interne statique pour votre machine virtuelle. Les machines virtuelles dans un réseau virtuel recevront automatiquement une adresse IP interne à partir d'une plage que vous spécifiez. Toutefois, dans certains cas, il peut être bon de spécifier une adresse IP statique pour une machine virtuelle en particulier. Par exemple, si votre machine virtuelle doit exécuter DNS ou fait office de contrôleur de domaine. Une adresse IP interne statique reste associée à la machine virtuelle même lorsque cette dernière se trouve en état d'arrêt/annulation de l’approvisionnement. 

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le [modèle de déploiement Resource Manager](virtual-networks-static-private-ip-arm-ps.md).
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Vérification de la disponibilité d'une adresse IP particulière
Pour vérifier si l’adresse IP *10.0.0.7* est disponible dans un réseau virtuel nommé *TestVNet*, exécutez la commande PowerShell suivante et vérifiez la valeur pour *IsAvailable* :

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Si vous voulez tester la commande ci-dessus dans un environnement sécurisé, suivez les instructions de l’article [Créer un réseau virtuel (classique)](virtual-networks-create-vnet-classic-pportal.md) pour créer un réseau virtuel nommé *TestVnet* et vérifiez qu’il utilise l’espace d’adressage *10.0.0.0/8*.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Spécification d’une adresse IP interne statique lors de la création d'une machine virtuelle
Le script PowerShell ci-dessous crée un service cloud nommé *TestService*, récupère une image auprès d’Azure, crée une machine virtuelle nommée *TestVM* dans le nouveau service cloud à partir de l’image récupérée, définit la machine virtuelle dans un sous-réseau nommé *Subnet-1*, puis définit *10.0.0.7* comme adresse IP interne statique pour la machine virtuelle :

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Récupération des informations d’adresse IP interne statique pour une machine virtuelle
Pour visualiser les informations d’adresse interne statique concernant la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell ci-après et examinez les valeurs des éléments *IpAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Suppression d’une adresse IP interne statique d'une machine virtuelle
Pour supprimer l’adresse IP interne statique ajoutée à la machine virtuelle par le biais du script ci-dessus, exécutez la commande PowerShell suivante :

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Ajout d’une adresse IP interne statique à une machine virtuelle existante
Pour ajouter une adresse IP interne statique à la machine virtuelle créée à l’aide du script ci-dessus, exécutez la commande suivante :

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes
[Adresse IP réservée](virtual-networks-reserved-public-ip.md)

[Adresses IP publiques de niveau d’instance (ILPIP)](virtual-networks-instance-level-public-ip.md)

[API REST d’adresse IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx)


