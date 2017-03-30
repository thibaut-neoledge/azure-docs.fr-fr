---
title: "Adresses IP publiques de niveau d’instance Azure (Classic) | Microsoft Docs"
description: "Découvrez les adresses IP publiques de niveau d’instance (ILPIP) et apprenez à les gérer à l’aide de PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: c233439b78fb01beaa3183b79ab633aeb9357ef0
ms.lasthandoff: 03/22/2017


---
# <a name="instance-level-public-ip-classic-overview"></a>Vue d’ensemble des adresses IP publiques de niveau d’instance (classique)
Une adresse IP publique de niveau d’instance (ILPIP) est une adresse IP publique que vous pouvez attribuer directement à une machine virtuelle ou instance de rôle de services cloud, plutôt qu’au service cloud dans lequel réside cette machine ou cette instance. Une adresse ILPIP ne remplace pas l’adresse IP virtuelle (VIP) affectée à votre service cloud. Il s’agit plutôt d’une adresse IP supplémentaire que vous pouvez utiliser pour vous connecter directement à votre machine virtuelle ou instance de rôle.

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Cet article traite du modèle de déploiement classique. Microsoft vous recommande de créer des machines virtuelles via Resource Manager. Assurez-vous que vous comprenez le fonctionnement des [adresses IP](virtual-network-ip-addresses-overview-classic.md) dans Azure.

![Différences entre les adresses IP publiques de niveau d’instance (ILPIP) et les adresses IP virtuelles (VIP)](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Comme le montre la figure 1, l’accès au service cloud s’effectue au moyen d’une adresse IP virtuelle, tandis que les différentes machines virtuelles sont accessibles normalement via VIP:&lt;numéro de port&gt;. L’attribution d’une adresse ILPIP à une machine virtuelle permet d’accéder à cette machine directement au moyen de cette adresse IP.

Quand vous créez un service cloud dans Azure, les enregistrements DNS A correspondants sont automatiquement créés de façon à autoriser l’accès au service par le biais d’un nom de domaine complet (FQDN) plutôt qu’avec l’adresse IP virtuelle proprement dite. Le même processus se produit pour une adresse ILPIP en permettant d’accéder à la machine virtuelle ou à l’instance de rôle par le nom de domaine complet plutôt que par l’intermédiaire de l’adresse ILPIP. Par exemple, si vous créez un service cloud sous le nom *contosoadservice* et que vous configurez un rôle web nommé *contosoweb* avec deux instances, Azure inscrit les enregistrements A suivants pour les instances :

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> Vous ne pouvez affecter qu’une seule adresse ILPIP par machine virtuelle ou instance de rôle. Vous pouvez utiliser jusqu’à 5 adresses ILPIP par abonnement. Les adresses ILPIP ne sont pas prises en charge pour les machines virtuelles à plusieurs cartes réseau.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Pourquoi demander une adresse ILPIP ?
Si vous souhaitez pouvoir vous connecter à votre machine virtuelle ou instance de rôle à l’aide d’une adresse IP qui vous est attribuée directement, demandez une adresse ILPIP pour votre machine virtuelle ou instance de rôle, au lieu d’utiliser l’adresse VIP:&lt;numéro-port&gt; du service cloud.

* **Mode FTP passif** : en affectant une adresse ILPIP à une machine virtuelle, cette dernière peut recevoir du trafic sur pratiquement n’importe quel port. Les points de terminaison ne sont pas requis pour que la machine virtuelle reçoive du trafic. Les adresses ILPIP autorisent notamment le mode FTP passif, où les ports sont sélectionnés de façon dynamique.
* **Adresse IP sortante** : le trafic sortant de la machine virtuelle présente l’adresse ILPIP en guise de source, identifiant ainsi de manière univoque la machine virtuelle auprès des entités externes.

> [!NOTE]
> Auparavant, une adresse ILPIP était appelée adresse IP publique (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Gérer une adresse ILPIP pour une machine virtuelle
Les tâches suivantes permettent de créer, d’affecter et de supprimer les ILPIPs des machines virtuelles :

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Comment demander une adresse ILPIP durant la création d’une machine virtuelle à l’aide de PowerShell
Le script PowerShell ci-dessous crée un service cloud nommé *FTPService*, récupère une image auprès d’Azure et crée une machine virtuelle nommée *FTPInstance* à partir de l’image récupérée, configure cette machine virtuelle pour qu’elle utilise une adresse ILPIP, puis ajoute la machine virtuelle au nouveau service :

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Récupération des informations d’adresse ILPIP pour une machine virtuelle
Pour visualiser les informations d’adresse ILPIP concernant la machine virtuelle créée avec le script précédent, exécutez la commande PowerShell ci-après et examinez les valeurs de *PublicIPAddress* et *PublicIPName* :

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Sortie attendue :
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :     Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Suppression d’une adresse ILPIP d’une machine virtuelle
Pour supprimer l’adresse ILPIP ajoutée à la machine virtuelle par le biais du script précédent, exécutez la commande PowerShell suivante :

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Ajout d’une adresse ILPIP à une machine virtuelle existante
Pour ajouter une adresse ILPIP à la machine virtuelle créée à l’aide du script précédent, exécutez la commande suivante :

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Gérer une adresse ILPIP pour une instance de rôle de services cloud

Pour ajouter une adresse ILPIP à une instance de rôle de services cloud, procédez comme suit :

1. Téléchargez le fichier .cscfg pour le service cloud en suivant les étapes décrites dans l’article [Configuration des services cloud](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg).
2. Mettez à jour le fichier .cscfg en ajoutant l’élément `InstanceAddress`. L’exemple suivant ajoute une adresse ILPIP nommée *MyPublicIP* à une instance de rôle nommé *WebRole1* : 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Chargez le fichier .cscfg pour le service cloud en suivant les étapes décrites dans l’article [Configuration des services cloud](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg).

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [l’adressage IP](virtual-network-ip-addresses-overview-classic.md) fonctionne dans le modèle de déploiement Classic.
* En savoir plus sur les [adresses IP réservées](virtual-networks-reserved-public-ip.md).

