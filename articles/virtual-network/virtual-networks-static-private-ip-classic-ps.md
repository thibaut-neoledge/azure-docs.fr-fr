<properties 
   pageTitle="Comment définir une adresse IP privée statique en mode classique à l’aide de PowerShell | azure.microsoft.com/ Azure"
   description="Présentation des adresses IP privées statiques (adresses IP dynamiques) et de leur gestion en mode classique et PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# Comment définir une adresse IP privée statique (classique) dans PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] Cet article traite du modèle de déploiement classique. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement Resource Manager](virtual-networks-static-private-ip-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les exemples de commandes PowerShell ci-dessous attendent un environnement simple déjà créé. Si vous souhaitez exécuter les commandes telles qu’elles sont affichées dans ce document, créez d’abord l’environnement de test décrit dans [Créer un réseau virtuel](virtual-networks-create-vnet-classic-netcfg-ps.md).

## Vérification de la disponibilité d'une adresse IP particulière
Pour vérifier si l’adresse IP *192.168.1.101* est disponible dans un réseau virtuel nommé *TestVnet*, exécutez la commande PowerShell suivante et vérifiez la valeur pour *IsAvailable* :

	Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Sortie attendue :

	IsAvailable          : True
	AvailableAddresses   : {}
	OperationDescription : Test-AzureStaticVNetIP
	OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
	OperationStatus      : Succeeded

## Comment spécifier une adresse IP privée statique lors de la création d’une machine virtuelle
Le script PowerShell ci-dessous crée un service cloud nommé *TestService*, récupère une image à partir d’Azure, crée une machine virtuelle nommée *DNS01* dans le nouveau service cloud à l’aide de l’image récupérée, définit la machine virtuelle dans un sous-réseau nommé *FrontEnd* et définit *192.168.1.7* comme adresse IP privée statique pour la machine virtuelle :

	New-AzureService -ServiceName TestService -Location "Central US"
	$image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
	  Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
	  Set-AzureSubnet –SubnetNames FrontEnd |
	  Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
	  New-AzureVM -ServiceName TestService –VNetName TestVNet

Sortie attendue :

	WARNING: No deployment found in service: 'TestService'.
	OperationDescription OperationId                          OperationStatus
	-------------------- -----------                          ---------------
	New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
	New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## Comment récupérer des informations d’adresse IP privée statique pour une machine virtuelle
Pour visualiser les informations d’adresse IP privée statique concernant la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell ci-après et examinez les valeurs pour *IpAddress* :

	Get-AzureVM -Name DNS01 -ServiceName TestService

Sortie attendue :

	DeploymentName              : TestService
	Name                        : DNS01
	Label                       : 
	VM                          : azure.microsoft.com/.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : Provisioning
	IpAddress                   : 192.168.1.7
	InstanceStateDetails        : Windows is preparing your computer for first use...
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : DNS01
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : rsR2-797
	AvailabilitySetName         : 
	DNSName                     : http://testservice000.cloudapp.net/
	Status                      : Provisioning
	GuestAgentStatus            : azure.microsoft.com/.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {azure.microsoft.com/.Compute.BGInfo}
	PublicIPAddress             : 
	PublicIPName                : 
	NetworkInterfaces           : {}
	ServiceName                 : TestService
	OperationDescription        : Get-AzureVM
	OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
	OperationStatus             : OK

## Suppression d’une adresse IP privée statique d’une machine virtuelle
Pour supprimer l’adresse IP privée statique ajoutée à la machine virtuelle par le biais du script ci-dessus, exécutez la commande PowerShell suivante :
	
	Get-AzureVM -ServiceName TestService -Name DNS01 |
	  Remove-AzureStaticVNetIP |
	  Update-AzureVM

Sortie attendue :

	OperationDescription OperationId                          OperationStatus
	-------------------- -----------                          ---------------
	Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## Comment ajouter une adresse IP privée statique à une machine virtuelle existante
Pour ajouter une adresse IP privée statique à la machine virtuelle créée à l’aide du script ci-dessus, exécutez la commande suivante :

	Get-AzureVM -ServiceName TestService -Name DNS01 |
	  Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
	  Update-AzureVM

Sortie attendue :

	OperationDescription OperationId                          OperationStatus
	-------------------- -----------                          ---------------
	Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## Étapes suivantes

- En savoir plus sur les [adresses IP publiques réservées](virtual-networks-reserved-public-ip.md).
- En savoir plus sur les [adresses IP publiques de niveau d’instance](virtual-networks-instance-level-public-ip.md).
- Consultez les [API REST d’adresse IP réservée](https://msdn.azure.microsoft.com/.com/library/azure/dn722420.aspx)

<!---HONumber=AcomDC_0817_2016-->