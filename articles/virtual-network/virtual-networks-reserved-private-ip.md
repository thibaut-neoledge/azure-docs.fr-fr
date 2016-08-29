<properties 
   pageTitle="Définition d’une adresse IP privée interne statique"
   description="Fonctionnement et gestion des adresses IP internes statiques (adresses IP dynamiques)"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# Comment définir une adresse IP privée interne statique à l’aide de PowerShell (Classic)
Dans la plupart des cas, il n’est pas nécessaire de spécifier une adresse IP interne statique pour votre machine virtuelle. Les machines virtuelles dans un réseau virtuel recevront automatiquement une adresse IP interne à partir d'une plage que vous spécifiez. Toutefois, dans certains cas, il peut être bon de spécifier une adresse IP statique pour une machine virtuelle en particulier. Par exemple, si votre machine virtuelle doit exécuter DNS ou fait office de contrôleur de domaine. Une adresse IP interne statique reste associée à la machine virtuelle même lorsque cette dernière se trouve en état d'arrêt/annulation de l’approvisionnement.

> [AZURE.IMPORTANT] Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le [modèle de déploiement Resource Manager](virtual-networks-static-private-ip-arm-ps.md).

## Vérification de la disponibilité d'une adresse IP particulière
Pour vérifier si l'adresse IP *10.0.0.7* est disponible dans un réseau virtuel nommé *TestVnet*, exécutez la commande PowerShell suivante et vérifiez la valeur de *IsAvailable* :

	Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

	IsAvailable          : True
	AvailableAddresses   : {}
	OperationDescription : Test-AzureStaticVNetIP
	OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
	OperationStatus      : Succeeded

>[AZURE.NOTE] Si vous souhaitez tester la commande ci-dessus dans un environnement sécurisé, suivez les instructions de l’article [Créer un réseau virtuel](virtual-networks-create-vnet-classic-portal.md) pour créer un réseau virtuel nommé *TestVnet* et assurez-vous qu'il utilise l’espace d’adressage *10.0.0.0/8*.

## Spécification d’une adresse IP interne statique lors de la création d'une machine virtuelle
Le script PowerShell ci-dessous crée un service cloud nommé *TestService*, extrait une image à partir d'Azure, puis crée une machine virtuelle nommée *TestVM* dans le nouveau service cloud à l'aide de l'image récupérée, définit la machine virtuelle dans un sous-réseau nommé *Subnet-1* et définit *10.0.0.7* comme adresse IP interne statique pour la machine virtuelle :

	New-AzureService -ServiceName TestService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzureSubnet –SubnetNames Subnet-1 `
	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## Récupération des informations d’adresse IP interne statique pour une machine virtuelle
Pour visualiser les informations d’adresse interne statique concernant la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell ci-après et examinez les valeurs des éléments *IpAddress* :

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

## Suppression d’une adresse IP interne statique d'une machine virtuelle
Pour supprimer l’adresse IP interne statique ajoutée à la machine virtuelle par le biais du script ci-dessus, exécutez la commande PowerShell suivante :
	
	Get-AzureVM -ServiceName TestService -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM

## Ajout d’une adresse IP interne statique à une machine virtuelle existante
Pour ajouter une adresse IP interne statique à la machine virtuelle créée à l’aide du script ci-dessus, exécutez la commande suivante :

	Get-AzureVM -ServiceName TestService000 -Name TestVM `
	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
	| Update-AzureVM

## Étapes suivantes

[Adresse IP réservée](virtual-networks-reserved-public-ip.md)

[Adresses IP publiques de niveau d’instance (ILPIP)](virtual-networks-instance-level-public-ip.md)

[API REST d’adresse IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=AcomDC_0817_2016-->