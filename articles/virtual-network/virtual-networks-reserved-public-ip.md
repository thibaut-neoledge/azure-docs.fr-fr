---
title: IP réservée | Microsoft Docs
description: Fonctionnement et gestion des adresses IP réservées
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial

---
# Vue d’ensemble des adresses IP réservées
Il existe deux catégories d’adresses IP dans Azure, les réservées et les dynamiques. Les adresses IP publiques gérées par Azure sont dynamiques par défaut. Cela signifie que l'adresse IP utilisée pour un service cloud donné (adresse IP virtuelle) ou pour accéder à une machine virtuelle ou à une instance de rôle directement (ILPIP) peut changer à tout moment, lorsque les ressources sont arrêtées ou désallouées.

Pour empêcher la modification des adresses IP, vous pouvez réserver une adresse IP. Les adresses IP réservées peuvent uniquement servir d’adresse IP virtuelle, garantissant que l'adresse IP utilisée pour le service cloud restera la même, et ce même si les ressources sont arrêtées ou désallouées. Par ailleurs, vous pouvez convertir une adresse IP dynamique existante utilisée comme adresse IP virtuelle en adresse IP réservée.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Découvrez comment réserver une adresse IP publique statique à l’aide du [modèle de déploiement Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Assurez-vous que vous comprenez le fonctionnement des [adresses IP](virtual-network-ip-addresses-overview-classic.md) dans Azure.

## Quand ai-je besoin d’une adresse IP réservée ?
* **Vous souhaitez vous assurer que l'adresse IP est réservée dans votre abonnement**. Si vous souhaitez réserver une adresse IP qui restera associée à votre abonnement en toute circonstance, vous devez utiliser une adresse IP publique réservée.
* **Vous souhaitez que votre adresse IP reste associée à votre service cloud, même lorsque les machines virtuelles sont arrêtées ou désallouées**. Si vous souhaitez que votre service soit accessible à l'aide d'une adresse IP qui ne change pas, même lorsque les machines virtuelles dans le service cloud sont arrêtées ou désallouées.
* **Vous souhaitez vous assurer que le trafic provenant d'Azure utilise une adresse IP prévisible**. Il se peut que la configuration de votre pare-feu local autorise uniquement le trafic provenant d'adresses IP spécifiques. En réservant une adresse IP, vous connaîtrez l'adresse IP source et n'aurez pas besoin de mettre à jour les règles de pare-feu suite à une modification d'adresse IP.

## Forum Aux Questions
1. Puis-je utiliser une adresse IP réservée pour tous les services Azure ?
   * Les adresses IP réservées peuvent être utilisées uniquement pour les machines virtuelles et les rôles d'instance de service cloud exposés par une adresse IP virtuelle.
2. Combien d’adresses IP réservées puis-je avoir ?
   * Pour le moment, tous les abonnements Azure peuvent utiliser 20 adresses IP réservées. Toutefois, vous pouvez demander à obtenir des adresses IP réservées supplémentaires. Pour en savoir plus, consultez la page [Abonnement Azure et limites du service](../azure-subscription-service-limits.md).
3. L’obtention d’adresses IP réservées est-elle payante ?
   * Pour plus d’informations sur la tarification, consultez la page [Tarification des adresses IP réservées](http://go.microsoft.com/fwlink/?LinkID=398482).
4. Comment réserver une adresse IP ?
   * Vous pouvez utiliser PowerShell ou [l’API REST de gestion Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) pour réserver une adresse IP dans une région particulière. Cette adresse IP réservée est associée à votre abonnement. Vous ne pouvez pas réserver une adresse IP à l'aide du portail de gestion.
5. Puis-je l'utiliser avec des réseaux virtuels basés sur un groupe d'affinités ?
   * Les adresses IP réservées sont uniquement prises en charge dans les réseaux virtuels régionaux. Elles ne sont pas prises en charge dans les réseaux virtuels associés à des groupes d’affinités. Pour plus d'informations sur l'association d'un réseau virtuel à une région ou un groupe d'affinités, consultez la page [À propos des réseaux virtuels et groupes d’affinités régionaux](virtual-networks-migrate-to-regional-vnet.md).

## Gestion des adresses IP virtuelles réservées
Avant de pouvoir utiliser une adresse IP réservée, vous devez l'ajouter à votre abonnement. Pour créer une adresse IP réservée à partir du pool d'adresses IP publiques disponibles dans la région *Centre des États-Unis*, exécutez la commande PowerShell suivante :

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

Toutefois, veuillez noter que vous ne pouvez pas spécifier quelle adresse IP vous souhaitez réserver. Pour voir quelles adresses IP sont réservées dans votre abonnement, exécutez la commande PowerShell suivante et notez les valeurs de *ReservedIPName* et *Address* :

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

Une fois une adresse IP réservée, elle reste associée à votre abonnement jusqu'à ce que vous la supprimiez. Pour supprimer l'adresse IP réservée ci-dessus, exécutez la commande PowerShell suivante :

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## Comment réserver l’adresse IP d’un service cloud existant
Vous pouvez réserver l’adresse IP d’un service cloud existant en ajoutant le paramètre *- ServiceName*. Pour réserver l’adresse IP d’un service cloud *TestService* dans les *États-Unis du Centre*, exécutez la commande PowerShell suivante :

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## Association d’une adresse IP réservée à un service cloud
Le script ci-dessous crée une adresse IP réservée, puis l’associe à un nouveau service cloud nommé *TestService*.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

> [!NOTE]
> Lorsque vous créez une adresse IP réservée à utiliser avec un service cloud, vous devez toujours faire référence à la machine virtuelle en utilisant *VIP:&lt;numéro de port>* pour les communications entrantes. Avoir une adresse IP réservée ne signifie pas que vous pouvez vous connecter directement à la machine virtuelle. L'adresse IP réservée est affectée au service cloud sur lequel la machine virtuelle a été déployée. Si vous souhaitez vous connecter à une machine virtuelle directement avec l’adresse IP, vous devez configurer une adresse IP publique de niveau de l'instance. Une adresse IP publique de niveau d'instance est un type d'adresse IP publique (appelée ILPIP) qui est affectée directement à votre machine virtuelle. Elle ne peut pas être réservée. Pour plus d’informations, consultez la page [Adresse IP publique de niveau d’instance (ILPIP)](virtual-networks-instance-level-public-ip.md).
> 
> 

## Suppression d’une adresse IP réservée dans un déploiement en cours d'exécution
Pour supprimer l’adresse IP réservée ajoutée à la machine virtuelle à l’aide du script ci-dessus, exécutez la commande PowerShell suivante :

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

> [!NOTE]
> Si vous supprimez une adresse IP réservée d’un déploiement en cours d'exécution, la réservation ne sera pas supprimée de votre abonnement. Cela libérera simplement l'adresse IP à utiliser par une autre ressource dans votre abonnement.
> 
> 

## Association d’une adresse IP réservée à un déploiement en cours d’exécution
Le script ci-dessous crée un service cloud nommé *TestService2* avec une nouvelle machine virtuelle nommée *TestVM2* et associe l’adresse IP réservée nommée *MyReservedIP* au service cloud.

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## Association d’une adresse réservée à un service cloud à l’aide d’un fichier de configuration de service
Vous pouvez aussi associer une IP réservée à un service cloud à l’aide d’un fichier de configuration de service (CSCFG). L’exemple de code xml ci-dessous indique comment configurer un service cloud pour l’utilisation d’une adresse IP réservée nommée *MyReservedIP* :

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## Étapes suivantes
* Découvrez comment [l’adressage IP](virtual-network-ip-addresses-overview-classic.md) fonctionne dans le modèle de déploiement Classic.
* En savoir plus sur [les adresses IP privées réservées](virtual-networks-reserved-private-ip.md).
* En savoir plus sur [les adresses IP publiques de niveau d’instance (ILPIP)](virtual-networks-instance-level-public-ip.md).

<!---HONumber=AcomDC_0824_2016-->