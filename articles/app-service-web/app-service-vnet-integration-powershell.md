<properties
	pageTitle="Connecter votre application à votre réseau virtuel à l’aide de PowerShell"
	description="Obtenir des instructions sur la façon de se connecter à des réseaux virtuels et de les utiliser à l’aide de PowerShell"
	services="app-service"
	documentationCenter=""
	authors="ccompy"
	manager="wpickett"
	editor="cephalin"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="ccompy"/>

# Connecter votre application à votre réseau virtuel à l’aide de PowerShell #

## Vue d'ensemble ##

Dans Azure App Service, vous pouvez connecter votre application (web, mobile ou API) à un réseau virtuel dans votre abonnement. Cette fonctionnalité est appelée « intégration au réseau virtuel ». Celle-ci ne doit pas être confondue avec la fonctionnalité App Service Environment, qui permet d’exécuter une instance d’Azure App Service dans votre réseau virtuel.

La fonctionnalité d’intégration de réseau virtuel a une interface utilisateur (IU) dans le nouveau portail que vous pouvez utiliser pour intégrer les réseaux virtuels déployés à l’aide du modèle de déploiement classique ou d’Azure Resource Manager. Pour en savoir plus sur la fonctionnalité, consultez l’article suivant : [Intégrer une application à un réseau Azure Virtual Network](web-sites-integrate-with-vnet.md).

Cet article ne couvre pas l’utilisation de l’interface utilisateur, mais explique plutôt comment permettre l’intégration à l’aide de PowerShell. Étant donné que les commandes pour chaque modèle de déploiement sont différentes, cet article contient une section propre à chaque modèle.

Avant de poursuivre la lecture de cet article, vérifiez ce qui suit :

- La version la plus récente du Kit de développement logiciel (SDK) Azure PowerShell est installée. L’installation peut se faire au moyen de Web Platform Installer.
- Une application dans Azure App Service est en cours d’exécution dans le niveau Standard ou Premium.

## Réseaux virtuels classiques ##

Cette section décrit trois tâches pour les réseaux virtuels qui utilisent le modèle de déploiement classique :

1. Connecter votre application à un réseau virtuel préexistant qui dispose d’une passerelle et est configuré pour la connectivité de point à site.
1. Mettre à jour des informations relatives à l’intégration au réseau virtuel pour votre application.
1. Déconnecter votre application du réseau virtuel.

### Connecter une application à un réseau virtuel classique ###

Pour connecter une application à un réseau virtuel, suivez ces trois étapes :

1. Déclarez à l’application web qu’elle doit joindre un réseau virtuel particulier. L’application génère un certificat transmis au réseau virtuel pour la connectivité de point à site.
1. Téléchargez le certificat de l’application web sur le réseau virtuel, puis récupérez l’URI du package VPN de point à site.
1. Mettez à jour la connexion de réseau virtuel des applications web avec l’URI du package de point à site.

Les premières et troisièmes étapes peuvent être exécutées entièrement à l’aide de scripts. Toutefois, la seconde étape nécessite une action manuelle unique au moyen du portail ou un accès permettant d’effectuer des actions **PUT** ou **PATCH** sur le point de terminaison ARM du réseau virtuel. Contactez le support Azure pour que cette option soit activée. Avant de commencer, vérifiez que vous disposez d’un réseau virtuel classique avec une connectivité de point à site activée et une passerelle déployée. Pour créer la passerelle et activer la connectivité de point à site, vous devez utiliser le portail comme décrit dans [Création d’une passerelle VPN][createvpngateway]

Le réseau virtuel classique doit se trouver dans le même abonnement que le plan App Service qui contient l’application faisant l’objet de l’intégration.

##### Configurer le Kit de développement logiciel (SDK) #####

Ouvrez une fenêtre PowerShell et configurez votre abonnement et votre compte Azure comme suit :

	Login-AzureRmAccount

Cette commande ouvre une invite vous demandant d’entrer vos informations d’identification Azure. Après vous être connecté, utilisez une des commandes suivantes pour sélectionner l’abonnement que vous souhaitez utiliser. Vérifiez que vous utilisez l’abonnement dans lequel sont situés votre réseau virtuel et votre plan App Service.

	Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

ou

	Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### Variables utilisées dans cet article #####

Pour simplifier les commandes ci-dessous, nous allons définir une variable PowerShell **$Configuration** avec la configuration spécifique.

Définissez une variable comme suit dans PowerShell avec les paramètres suivants :

	$Configuration = @{}
	$Configuration.WebAppResourceGroup = "[Your web app resource group]"
	$Configuration.WebAppName = "[Your web app name]"
	$Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
	$Configuration.VnetResourceGroup = "[Your vnet resource group]"
	$Configuration.VnetName = "[Your vnet name]"

L’emplacement de l’application doit être spécifié sans espace. Par exemple, « West US » devient westus.

	$Configuration.WebAppLocation = "[Your web app Location]"

L’élément suivant indique où le certificat doit être écrit. Il doit s’agir d’un chemin d’accès en écriture sur votre ordinateur local. Pensez à inclure .cer à la fin.

	$Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

Pour voir ce que vous définissez, tapez **$Configuration**.

	> $Configuration

	Name                           Value
	----                           -----
	GeneratedCertificatePath       C:\vnetcert.cer
	VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
	WebAppResourceGroup            vnetdemo-rg
	VnetResourceGroup              testase1-rg
	VnetName                       TestNetwork
	WebAppName                     vnetintdemoapp
	WebAppLocation                 centralus

Le reste de cette section suppose que vous disposez d’une variable créée comme indiqué ci-dessus.

##### Déclarer le réseau virtuel à l’application #####

Utilisez la commande suivante pour indiquer à l’application qu’elle doit utiliser ce réseau virtuel particulier. L’application génère ensuite les certificats nécessaires :

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Si cette commande réussit, **$vnet** doit inclure une variable **Properties**. La variable **Properties** doit contenir l’empreinte numérique et les données du certificat.

##### Télécharger le certificat de l’application web dans le réseau virtuel #####

Une étape unique manuelle est requise pour chaque combinaison réseau virtuel/abonnement. Autrement dit, si vous connectez les applications de l’abonnement A au réseau virtuel A, vous ne devez effectuer cette opération qu’une seule fois, quel que soit le nombre d’applications que vous configurez. Si vous ajoutez une nouvelle application à un autre réseau virtuel, vous devez effectuer de nouveau cette opération. Cela est dû au fait qu’un ensemble de certificats est généré au niveau de l’abonnement dans Azure App Service, une fois pour chaque réseau virtuel auquel les applications se connectent.

Les certificats doivent déjà être définis si vous avez suivi ces étapes ou si vous avez effectué une intégration avec le même réseau virtuel à l’aide du portail.

La première étape consiste à générer le fichier .cer. La deuxième étape est de télécharger le fichier .cer vers votre réseau virtuel. Pour générer le fichier .cer à partir de l’appel d’API de l’étape précédente, exécutez les commandes suivantes.

	$certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
	[System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

Le certificat se trouve dans l’emplacement spécifié avec **$Configuration.GeneratedCertificatePath**

Pour télécharger le certificat manuellement, utilisez le nouveau [portail Azure][azureportal] et accédez à **Réseau virtuel (classique)** -> **Connexions VPN** -> **Point à site** -> **Gérer les certificats**. À partir de là, téléchargez votre certificat.

##### Obtenir le package de point à site #####

L’étape suivante de la configuration d’une connexion de réseau virtuel sur une application web consiste à obtenir le package de point à site et à le fournir à votre application web.

Enregistrez le modèle suivant dans un fichier appelé GetNetworkPackageUri.json situé sur votre ordinateur, par exemple dans C:\\Azure\\Templates\\GetNetworkPackageUri.json.

	{
		"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"certData": {
				"type": "string"
			},
			"certThumbprint": {
				"type": "string"
			},
			"networkName": {
				"type": "string"
			}
		},
		"variables": {
			"legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
			},
			"resources": [
			],
		"outputs" : {
			"PackageUri" :
			{
			"value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
			}
		}
	}


Définissez les paramètres d’entrée :

	$parameters = @{"certData" = $vnet.Properties.certBlob ;
	certThumbprint = $vnet.Properties.certThumbprint ;
	"networkName" = $Configuration.VnetName }

Appelez le script :

	$output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


La variable **$output.Outputs.packageUri** contient maintenant l’URI de package à donner à votre application web.

##### Téléchargement du package de point à site dans votre application #####

La dernière étape consiste à fournir ce package à l’application. Exécutez tout simplement la commande suivante :

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

Si un message vous demande de confirmer que vous remplacez une ressource existante, dites que c’est bien le cas.

Une fois cette commande réussie, votre application doit maintenant être connectée au réseau virtuel. Pour confirmer la réussite, accédez à la console de votre application et tapez la commande suivante :

	SET WEBSITE_

S’il existe une variable d’environnement appelée WEBSITE\_VNETNAME ayant une valeur correspondant au nom du réseau virtuel cible, toutes les configurations ont réussi.

### Mettre à jour des informations relatives à l’intégration au réseau virtuel classique ###

Pour mettre à jour ou resynchroniser vos informations, répétez simplement les étapes suivies lorsque vous avez créé l’intégration en premier lieu. à savoir :

1. Définir vos informations de configuration.
1. Déclarer le réseau virtuel à l’application.
1. Obtenir le package de point à site.
1. Télécharger le package de point à site dans votre application.

### Déconnecter votre application d’un réseau virtuel classique ###

Pour déconnecter l’application, vous avez besoin des informations de configuration définies lors de l’intégration au réseau virtuel. Ces informations et une commande unique sont nécessaires pour déconnecter votre application de votre réseau virtuel.

	$vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## Réseaux virtuels Resource Manager ##

Les réseaux virtuels Resource Manager disposent d’API Azure Resource Manager, qui simplifient certains processus si on les compare aux réseaux virtuels classiques. Nous avons un script qui vous permet d’effectuer les tâches suivantes :

- Créer un réseau virtuel Resource Manager et y intégrer votre application
- Créer une passerelle, configurer la connectivité de point à site dans un réseau virtuel Resource Manager préexistant, puis y intégrer votre application.
- Intégrer votre application à un réseau virtuel Resource Manager préexistant disposant d’une passerelle et sur lequel la connectivité de point à site est activée.
- Déconnecter votre application du réseau virtuel.

### Script d’intégration App Service pour les réseaux virtuels Resource Manager ###

Copiez le script suivant et enregistrez-le dans un fichier. Si vous ne souhaitez pas utiliser le script, étudiez-le pour savoir comment configurer un réseau virtuel Resource Manager.


    function ReadHostWithDefault($message, $default)
    {
    	$result = Read-Host "$message [$default]"
    	if($result -eq "")
	    {
			$result = $default
	    }
		    return $result
    	}

	function PromptCustom($title, $optionValues, $optionDescriptions)
	{
	    Write-Host $title
	    Write-Host
	    $a = @()
	    for($i = 0; $i -lt $optionValues.Length; $i++){
		    Write-Host "$($i+1))" $optionDescriptions[$i]
	    }
	    Write-Host

	    while($true)
	    {
		    Write-Host "Choose an option: "
		    $option = Read-Host
		    $option = $option -as [int]

		    if($option -ge 1 -and $option -le $optionValues.Length)
		    {
			    return $optionValues[$option-1]
		    }
	    }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
	    $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
	    $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
	    $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
	    $result = $host.ui.PromptForChoice($title, $message, $options, $default)
	    return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
	    Write-Host "Creating a new VNET"
	    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
	    New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
	    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
	    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

	    Write-Host "Creating a public IP address for this VNET"
	    $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
	    $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

	    Write-Host "Adding a root certificate to this VNET"
	    $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

	    Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
	    New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
	    Write-Host "Adding a new Vnet"
	    Write-Host
	    $vnetName = Read-Host "Specify a name for this Virtual Network"

	    $vnetGatewayName="$($vnetName)-gateway"
	    $vnetIpName="$($vnetName)-ip"
	    $vnetIpConfigName="$($vnetName)-ipconf"

	    # Virtual Network settings
	    $vnetAddressSpace="10.0.0.0/8"
	    $vnetGatewayAddressSpace="10.5.0.0/16"
	    $vnetPointToSiteAddressSpace="172.16.0.0/16"

	    $changeRequested = 0
	    $resourceGroupName = $webAppResourceGroup

	    while($changeRequested -eq 0)
	    {
		    Write-Host
		    Write-Host "Currently, I will create a VNET with the following settings:"
		    Write-Host
		    Write-Host "Virtual Network Name: $vnetName"
		    Write-Host "Resource Group Name:  $resourceGroupName"
		    Write-Host "Gateway Name: $vnetGatewayName"
		    Write-Host "Vnet IP name: $vnetIpName"
		    Write-Host "Vnet IP config name:  $vnetIpConfigName"
		    Write-Host "Address Space:$vnetAddressSpace"
		    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
		    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
		    Write-Host
		    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

		    if($changeRequested -eq 0)
		    {
			    $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
			    $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
			    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
			    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
			    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
			    $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
			    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
			    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
		    }
	    }

	    $ErrorActionPreference = "Stop";

	    # We create the virtual network and add it here. The way this works is:
	    # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
	    # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
	    # 3) Get the VPN package from the gateway and pass it back to the App.

	    $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
	    $location = $webApp.Location

	    Write-Host "Creating App association to VNET"
	    $propertiesObject = @{
	     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
	    }
	    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

	    CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

	    CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

	    Write-Host "Retrieving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
	    }

	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

	    Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
		$ErrorActionPreference = "Stop";

		# At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
		Write-Host "Getting App information"
		$webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$location = $webApp.Location

		$webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$currentVnet = $webAppConfig.Properties.VnetName
		if($currentVnet -ne $null -and $currentVnet -ne "")
		{
			Write-Host "Currently connected to VNET $currentVnet"
		}

		# Display existing vnets
		$vnets = Get-AzureRmVirtualNetwork
		$vnetNames = @()
		foreach($vnet in $vnets)
		{
			$vnetNames += $vnet.Name
	    }

	    Write-Host
	    $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

	    # We need to check if this VNET is able to be joined to a App, based on following criteria
		    # If there is no gateway, we can create one.
		    # If there is a gateway:
			    # It must be of type Vpn
			    # It must be of VpnType RouteBased
			    # If it doesn't have the right certificate, we will need to add it.
			    # If it doesn't have a point-to-site range, we will need to add it.

	    $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

	    if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
	    {
			$ErrorActionPreference = "Continue";
		    # There is no gateway. We need to create one.
		    Write-Host "This Virtual Network has no gateway. I will need to create one."

		    $vnetName = $vnet.Name
		    $vnetGatewayName="$($vnetName)-gateway"
		    $vnetIpName="$($vnetName)-ip"
		    $vnetIpConfigName="$($vnetName)-ipconf"

		    # Virtual Network settings
		    $vnetAddressSpace="10.0.0.0/8"
		    $vnetGatewayAddressSpace="10.5.0.0/16"
		    $vnetPointToSiteAddressSpace="172.16.0.0/16"

		    $changeRequested = 0

		    Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
		    foreach($subnet in $vnet.Subnets)
		    {
			    Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
		    }

		    $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

		    while($changeRequested -eq 0)
		    {
			    Write-Host
			    Write-Host "Currently, I will create a VNET gateway with the following settings:"
			    Write-Host
			    Write-Host "Virtual Network Name: $vnetName"
			    Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
			    Write-Host "Gateway Name: $vnetGatewayName"
			    Write-Host "Vnet IP name: $vnetIpName"
			    Write-Host "Vnet IP config name:  $vnetIpConfigName"
			    Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
			    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
			    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
			    Write-Host
			    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

			    if($changeRequested -eq 0)
			    {
				    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
				    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
				    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
				    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
				    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
			    }
		    }

		    $ErrorActionPreference = "Stop";

		    Write-Host "Creating App association to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }

		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

		    # If there is no gateway subnet, we need to create one.
		    if($gatewaySubnet -eq $null)
		    {
			    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
			    $vnet.Subnets.Add($gatewaySubnet);
			    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
		    }

		    CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
	    }
	    else
	    {
		    $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
		    $gatewayResourceGroup = $uriParts[4]
		    $gatewayName = $uriParts[8]

		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

		    # validate gateway types, etc.
		    if($gateway.GatewayType -ne "Vpn")
		    {
			    Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
			    return
		    }

		    if($gateway.VpnType -ne "RouteBased")
		    {
			    Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
			    return
		    }

		    if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
		    {
			    Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
			    $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
			    Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
		    }

		    Write-Host "Creating App association to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }

		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

		    # We need to check if the certificate here exists in the gateway.
		    $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

		    $certFound = $false
		    foreach($certificate in $certificates)
		    {
			    if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
			    {
				    $certFound = $true
				    break
			    }
		    }

		    if(-not $certFound)
		    {
			    Write-Host "Adding certificate"
			    Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
		    }
	    }

	    # Now finish joining by getting the VPN package and giving it to the App
	    Write-Host "Retrieving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
	    }

	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

	    Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
	    $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    $currentVnet = $webAppConfig.Properties.VnetName
	    if($currentVnet -ne $null -and $currentVnet -ne "")
	    {
		    Write-Host "Currently connected to VNET $currentVnet"

		    Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    }
	  	  else
	    {
	  	  Write-Host "Not connected to a VNET."
	    }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
	    Write-Error "No subscriptions bound to this account."
	    return
    }

    if($subs.Length -eq 1)
    {
	    $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
	    $subscriptionChoices = @()
	    $subscriptionValues = @()

	    foreach($subscription in $subs){
	    $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
	    $subscriptionValues += ($subscription.SubscriptionId);
	    }

	    $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
		AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
	    AddExistingVnet $subscriptionId $resourceGroup $appName
    }
	if($option -eq 2)
    {
	    RemoveVnet $subscriptionId $resourceGroup $appName
    }

Enregistrez une copie du script. Dans cet article, il est appelé V2VnetAllinOne.ps1, mais vous pouvez utiliser un autre nom. Ce script n’inclut aucun argument. Vous devez simplement l’exécuter. Le script commence par vous inviter à vous connecter. Lorsque vous êtes connecté, le script obtient des informations détaillées sur votre compte et renvoie une liste des abonnements. Sans compter la demande de vos informations d’identification, l’exécution de script initiale se présente comme suit :

	PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
	Please Login

	Environment           : AzureCloud
	Account               : ccompy@microsoft.com
	TenantId              : 722278f-fef1-499f-91ab-2323d011db47
	SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
	CurrentStorageAccount :

	Choose a subscription

	1) Demo Subscription (af5358e1-acac-2c90-a9eb-722190abf47a)
	2) MS Test (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
	3) Purple Demo Subscription (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

	Choose an option:
	3

	Account      : ccompy@microsoft.com
	Environment  : AzureCloud
	Subscription : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d
	Tenant       : 722278f-fef1-499f-91ab-2323d011db47

	Please enter the Resource Group of your App: hcdemo-rg
	Please enter the Name of your App: v2vnetpowershell
	What do you want to do?

	1) Add a NEW Virtual Network to an App
	2) Add an EXISTING Virtual Network to an App
	3) Remove a Virtual Network from an App

Le reste de cette section décrit chacune de ces trois options.

### Créer et intégrer un réseau virtuel Resource Manager ###

Pour créer un nouveau réseau virtuel qui utilise le modèle de déploiement Resource Manager et l’intégrer à votre application, sélectionnez **1) Ajouter un NOUVEAU réseau virtuel à une application**. Vous êtes invité à entrer le nom du réseau virtuel. Dans mon cas, comme vous pouvez le voir dans les paramètres suivants, j’ai utilisé le nom v2pshell.

Le script fournit les détails concernant le réseau virtuel est en cours de création. Si je le souhaite, je peux modifier les valeurs. Dans cet exemple d’exécution, j’ai créé un réseau virtuel ayant les paramètres suivants :

	Virtual Network Name:         v2pshell
	Resource Group Name:          hcdemo-rg
	Gateway Name:                 v2pshell-gateway
	Vnet IP name:                 v2pshell-ip
	Vnet IP config name:          v2pshell-ipconf
	Address Space:                10.0.0.0/8
	Gateway Address Space:        10.5.0.0/16
	Point-To-Site Address Space:  172.16.0.0/12

	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):

Si vous souhaitez modifier une des valeurs, tapez **Y** et apportez les modifications souhaitées. Lorsque vous êtes satisfait des paramètres du réseau virtuel, tapez **N** ou appuyez simplement sur Entrée lorsque vous êtes invité à modifier les paramètres. À partir de là et jusqu’à la fin de son exécution, le script vous indique certaines des étapes en cours jusqu’à la création de la passerelle de réseau virtuel. Cette étape peut prendre une heure. Ce script n’affiche aucun indicateur de progression, mais il vous indique quand la passerelle a été créée.

Lorsque le script est terminé, il affiche **Terminé**. À ce stade, vous disposez d’un réseau virtuel Resource Manager qui a le nom et les paramètres que vous avez sélectionnés. Ce nouveau réseau virtuel sera également intégré à votre application.

### Intégrer votre application à un réseau virtuel Resource Manager préexistant ###

Lorsque vous intégrez un réseau virtuel préexistant, si vous fournissez un réseau virtuel Resource Manager ne disposant pas de passerelle ou de connectivité de point à site, le script configurera ces éléments. Si le réseau virtuel possède déjà ces éléments, l’intégration de l’application commence directement. Pour démarrer ce processus, sélectionnez l’option **2) Ajouter un réseau virtuel EXISTANT à une application**.

Cette option fonctionne uniquement si vous disposez d’un réseau virtuel Resource Manager se trouvant dans le même abonnement que votre application. Après avoir sélectionné l’option, une liste de vos réseaux virtuels Resource Manager s’affiche.

	Select a VNET to integrate with

	1) v2demonetwork
	2) v2pshell
	3) v2vnetintdemo
	4) v2asenetwork
	5) v2pshell2

	Choose an option:
	5

Sélectionnez le réseau virtuel que vous souhaitez intégrer. Si vous disposez déjà d’une passerelle sur laquelle la connectivité de point à site est activée, le script intègre directement votre application à votre réseau virtuel. Si vous ne disposez pas d’une passerelle, vous devez spécifier le sous-réseau associé. Votre sous-réseau de passerelle doit être situé dans votre espace d’adressage de réseau virtuel. Il ne peut pas être situé dans un autre sous-réseau. Si vous avez un réseau virtuel sans passerelle et que vous exécutez cette étape, voici ce que vous obtenez :

	This Virtual Network has no gateway. I will need to create one.
	Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
	default: 172.16.0.0/24
	Please choose a GatewaySubnet address space: 172.16.1.0/26

Dans cet exemple, j’ai créé une passerelle de réseau virtuel ayant les paramètres suivants :

	Virtual Network Name:         v2pshell2
	Resource Group Name:          vnetdemo-rg
	Gateway Name:                 v2pshell2-gateway
	Vnet IP name:                 v2pshell2-ip
	Vnet IP config name:          v2pshell2-ipconf
	Address Space:                172.16.0.0/16
	Gateway Address Space:        172.16.1.0/26
	Point-To-Site Address Space:  172.16.0.0/12

	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):
	Creating App association to VNET

Si vous le souhaitez, vous pouvez modifier ces paramètres. Dans le cas contraire, appuyez sur Entrée pour que le script crée votre passerelle et attache votre application au réseau virtuel. Gardez à l’esprit que la création de la passerelle peut prendre jusqu’à une heure. Lorsque toutes les opérations sont terminées, le script indique **Terminé**.

### Se déconnecter de votre application à partir d’un réseau virtuel Resource Manager ###

La déconnexion de votre application de votre réseau virtuel ne désactive ni la passerelle ni la connectivité de point à site. Vous pouvez donc utiliser ces dernières à d’autres fins. Seule l’application indiquée est déconnectée. Pour effectuer cette action, sélectionnez **3) Supprimer un réseau virtuel d’une application**. Vous obtenez ceci :

	Currently connected to VNET v2pshell

	Confirm
	Are you sure you want to delete the following resource:
	/subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
	hell/virtualNetworkConnections/v2pshell
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Bien que le script indique la suppression, il ne supprime pas le réseau virtuel. Il supprime simplement l’intégration. Une fois que vous avez confirmé que c’est bien ce que vous voulez faire, la commande est traitée rapidement et indique **True** lorsqu’elle est terminée.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com

<!---HONumber=AcomDC_0518_2016-->