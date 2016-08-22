<properties 
   pageTitle="Adresses IP publiques de niveau d’instance (ILPIP) | Microsoft Azure"
   description="Description des adresses IP publiques (PIP) de niveau d’instance (ILPIP) et de leur mode de gestion"
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
   ms.date="02/10/2016"
   ms.author="jdial" />

# Vue d’ensemble des adresses IP publiques de niveau d’instance
Une adresse IP publique de niveau d’instance (ILPIP) est une adresse IP publique que vous pouvez attribuer directement à votre machine virtuelle ou instance de rôle, plutôt qu’au service cloud dans lequel réside cette machine ou cette instance. Elle ne remplace pas l’adresse IP virtuelle (VIP) affectée à votre service cloud. Il s’agit plutôt d’une adresse IP supplémentaire que vous pouvez utiliser pour vous connecter directement à votre machine virtuelle ou instance de rôle.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Assurez-vous que vous comprenez le fonctionnement des [adresses IP](virtual-network-ip-addresses-overview-classic.md) dans Azure.

>[AZURE.NOTE] Auparavant, une adresse ILPIP était appelée PIP (signifiant adresse IP publique).

![Différences entre les adresses IP publiques de niveau d’instance (ILPIP) et les adresses IP virtuelles (VIP)](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Comme illustré à la Figure 1, le service cloud est accessible à l’aide d’une adresse IP virtuelle, tandis que les différentes machines virtuelles sont accessibles normalement par le biais de VIP:&lt;,numéro de port&gt;. L’attribution d’une adresse ILPIP à une machine virtuelle permet d’accéder à cette machine directement au moyen de cette adresse IP.

Quand vous créez un service cloud dans Azure, les enregistrements DNS A correspondants sont automatiquement créés de façon à autoriser l’accès au service par le biais d’un nom de domaine complet (FQDN) plutôt qu’avec l’adresse IP virtuelle proprement dite. Le même processus se produit pour l’adresse ILPIP en permettant d’accéder à la machine virtuelle ou à l’instance de rôle par le nom de domaine complet plutôt que par l’intermédiaire de l’adresse ILPIP. Par exemple, si vous créez un service cloud nommé *contosoadservice*, et que vous configurez un rôle web nommé *contosoweb* avec deux instances, Azure inscrit les enregistrements A suivants pour les instances :

- contosoweb\_IN\_0.contosoadservice.cloudapp.NET
- contosoweb\_IN\_1.contosoadservice.cloudapp.net

>[AZURE.NOTE] Vous ne pouvez affecter qu’une seule adresse ILPIP par machine virtuelle ou instance de rôle. Vous pouvez utiliser jusqu’à 5 adresses ILPIP par abonnement. Pour l’instant, les adresses ILPIP ne sont pas prises en charge pour les machines virtuelles équipées de plusieurs cartes d’interface réseau.

## Pourquoi dois-je demander une adresse ILPIP ?
Si vous souhaitez pouvoir vous connecter à votre machine virtuelle ou instance de rôle à l’aide d’une adresse IP attribuée directement, demandez une adresse ILPIP pour votre machine virtuelle ou instance de rôle, au lieu d’utiliser l’adresse VIP:&lt;numéro-port&gt; du service cloud.
- **FTP passif** : avec une adresse ILPIP sur votre machine virtuelle, vous pouvez recevoir du trafic sur n’importe quel port et vous n’avez besoin d’ouvrir aucun point de terminaison pour recevoir le trafic. Cette possibilité autorise notamment le mode FTP passif, où les ports sont sélectionnés de façon dynamique.
- **Adresse IP sortante** : le trafic sortant de la machine virtuelle présente l’adresse ILPIP en guise de source, identifiant ainsi de manière univoque la machine virtuelle auprès des entités externes.

## Demande d’une adresse ILPIP durant la création d’une machine virtuelle
Le script PowerShell ci-dessous crée un service cloud nommé *FTPService*, puis récupère une image à partir d’Azure et crée une machine virtuelle nommée *FTPInstance* à l’aide de l’image récupérée, configure cette machine virtuelle pour qu’elle utilise une adresse ILPIP et ajoute la machine au nouveau service :

	New-AzureService -ServiceName FTPService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## Récupération des informations d’adresse ILPIP pour une machine virtuelle
Pour visualiser les informations d’adresse ILPIP concernant la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell ci-après et examinez les valeurs des éléments *PublicIPAddress* et *PublicIPName* :

	Get-AzureVM -Name FTPInstance -ServiceName FTPService

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
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 104.43.142.188
	PublicIPName                : ftpip
	NetworkInterfaces           : {}
	ServiceName                 : FTPService
	OperationDescription        : Get-AzureVM
	OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
	OperationStatus             : OK

## Suppression d’une adresse ILPIP d’une machine virtuelle
Pour supprimer l’adresse ILPIP ajoutée à la machine virtuelle par le biais du script ci-dessus, exécutez la commande PowerShell suivante :
	
	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Remove-AzurePublicIP `
	| Update-AzureVM

## Ajout d’une adresse ILPIP à une machine virtuelle existante
Pour ajouter une adresse ILPIP à la machine virtuelle créée à l’aide du script ci-dessus, exécutez la commande suivante :

	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Set-AzurePublicIP -PublicIPName ftpip2 `
	| Update-AzureVM

## Association d’une adresse ILPIP à une machine virtuelle à l’aide d’un fichier de configuration de service
Vous pouvez également associer une adresse ILPIP à une machine virtuelle au moyen d’un fichier de configuration de service (CSCFG). L’exemple de code XML ci-dessous indique comment configurer un service cloud afin qu’il utilise une adresse ILPIP nommée *MyPublicIP* pour une instance de rôle :
	
	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
      <NetworkConfiguration>
	    <VirtualNetworkSite name="VNet"/>
	    <AddressAssignments>
	      <InstanceAddress roleName="VMRolePersisted">
	        <Subnets>
	          <Subnet name="Subnet1"/>
	          <Subnet name="Subnet2"/>
	        </Subnets>
	        <PublicIPs>
	          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
	        </PublicIPs>
	      </InstanceAddress>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## Étapes suivantes

- Découvrez comment [l’adressage IP](virtual-network-ip-addresses-overview-classic.md) fonctionne dans le modèle de déploiement Classic.

- En savoir plus sur les [adresses IP réservées](virtual-networks-reserved-public-ip.md).
 

<!---HONumber=AcomDC_0810_2016-->