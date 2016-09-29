<properties
   pageTitle="Déployer des machines virtuelles à plusieurs cartes réseau à l’aide de PowerShell dans Resource Manager | Microsoft Azure"
   description="Apprenez à déployer des machines virtuelles à plusieurs cartes réseau à l’aide de PowerShell dans Resource Manager."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#Déployer des machines virtuelles à plusieurs cartes réseau à l’aide de PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Actuellement, un même groupe à haute disponibilité ne peut pas avoir des machines virtuelles avec une seule carte réseau et des machines virtuelles avec plusieurs cartes réseau. Par conséquent, vous devez implémenter les serveurs principaux dans un groupe de ressources différent de celui des autres composants. Les étapes ci-dessous utilisent un groupe de ressources nommé *IaaSStory* pour le groupe de ressources principal et *IaaSStory-BackEnd* pour les serveurs principaux.

## Composants requis

Avant de déployer les serveurs principaux, vous devez déployer le groupe de ressources principal avec toutes les ressources nécessaires pour ce scénario. Pour déployer ces ressources, procédez comme suit.

1. Accédez à la [page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Dans la page de modèle, à droite du **groupe de ressources parent**, cliquez sur **Déployer dans Azure**.
3. Si nécessaire, modifiez les valeurs de paramètre, puis suivez les étapes du portail Azure en version préliminaire pour déployer le groupe de ressources.

> [AZURE.IMPORTANT] Assurez-vous que vos noms de compte de stockage sont uniques. Vous ne pouvez pas avoir des noms de compte de stockage en double dans Azure.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Déploiement des machines virtuelles principales

Les machines virtuelles principales dépendent de la création de ressources répertoriées ci-dessous.

- **Compte de stockage pour les disques de données**. Pour optimiser les performances, les disques de données sur les serveurs de base de données utilisent la technologie de disque SSD, qui requiert un compte de stockage Premium. Assurez-vous que l’emplacement Azure de déploiement prend en charge le stockage Premium.
- **Cartes réseau**. Chaque machine virtuelle a deux cartes réseau, une pour l’accès à la base de données et l’autre pour la gestion.
- **Groupe à haute disponibilité**. Tous les serveurs de base de données sont ajoutés à un groupe à haute disponibilité, afin de garantir qu’au moins une des machines virtuelles est en cours d’exécution lors de la maintenance.

### Étape 1 : démarrer votre script

Vous pouvez télécharger le script PowerShell complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-ps.ps1). Suivez les étapes ci-dessous pour modifier le script afin qu’il fonctionne dans votre environnement.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Modifiez les valeurs des variables suivantes selon votre groupe de ressources existant déployé ci-dessus dans les [Conditions préalables](#Prerequisites).

		$existingRGName        = "IaaSStory"
		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"
		$remoteAccessNSGName   = "NSG-RemoteAccess"
		$stdStorageAccountName = "wtestvnetstoragestd"

2. Modifiez les valeurs des variables suivantes en fonction des valeurs que vous souhaitez utiliser pour le déploiement de votre serveur principal.

		$backendRGName         = "IaaSStory-Backend"
		$prmStorageAccountName = "wtestvnetstorageprm"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$publisher             = "MicrosoftSQLServer"
		$offer                 = "SQL2014SP1-WS2012R2"
		$sku                   = "Standard"
		$version               = "latest"
		$vmNamePrefix          = "DB"
		$osDiskPrefix          = "osdiskdb"
		$dataDiskPrefix        = "datadisk"
		$diskSize	           = "120"	
		$nicNamePrefix         = "NICDB"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

3. Récupérer les ressources existantes nécessaires au déploiement.

		$vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
		$backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
		$remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
		$stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### Étape 2 : création des ressources nécessaires pour vos machines virtuelles

Vous devez créer un groupe de ressources, un compte de stockage pour les disques de données et un groupe à haute disponibilité pour toutes les machines virtuelles. Vous avez également besoin des informations d’identification du compte d’administrateur local pour chaque machine virtuelle. Pour créer ces ressources, exécutez les étapes suivantes.

1. Créez un groupe de ressources.

		New-AzureRmResourceGroup -Name $backendRGName -Location $location

2. Créez un compte de stockage Premium dans le groupe de ressources créé ci-dessus.

		$prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
			-ResourceGroupName $backendRGName -Type Premium_LRS -Location $location

3. Créez un groupe à haute disponibilité.

		$avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location

4. Récupérez les informations d’identification du compte d’administrateur local à utiliser pour chaque machine virtuelle.

		$cred = Get-Credential -Message "Type the name and password for the local administrator account."

### Étape 3 : création des cartes réseau et des machines virtuelles principales

Vous devez utiliser une boucle pour créer le nombre de machines virtuelles que vous voulez et créer les cartes réseau et les machines virtuelles nécessaires au sein de la boucle. Pour créer ces cartes réseau et ces machines virtuelles, exécutez les étapes suivantes.

1. Lancez une boucle `for` pour répéter les commandes afin de créer une machine virtuelle et deux cartes réseau, autant de fois que nécessaire, selon la valeur de la variable `$numberOfVMs`.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Créez la carte réseau utilisée pour l’accès à la base de données.

		    $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
		    $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
		    $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1

3. Créez la carte réseau utilisée pour l’accès à distance. Notez que cette carte réseau a un groupe de sécurité réseau associé.

		    $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
		    $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
		    $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
				-NetworkSecurityGroupId $remoteAccessNSG.Id

4. Créez l'objet `vmConfig`.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id

5. Créez deux disques de données pour chaque machine virtuelle. Notez que les disques de données résident dans le compte de stockage Premium créé précédemment.

		    $dataDisk1Name = $vmName + "-" + $osDiskPrefix + "-1"    
		    $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
				-VhdUri $data1VhdUri -CreateOption empty -Lun 0

		    $dataDisk2Name = $vmName + "-" + $dataDiskPrefix + "-2"    
		    $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
				-VhdUri $data2VhdUri -CreateOption empty -Lun 1

6. Configurez le système d’exploitation et l’image à utiliser pour la machine virtuelle.

		    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version

7. Ajoutez les deux cartes réseau créées ci-dessus à l'objet `vmConfig`.

		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id

8. Créez le disque de système d’exploitation et la machine virtuelle. Notez le `}` qui termine la boucle `for`.

		    $osDiskName = $vmName + "-" + $osDiskSuffix
		    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
		    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
		    New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
		}

### Étape 4 : exécution du script

Maintenant que vous avez téléchargé et modifié le script selon vos besoins, exécutez le script pour créer les machines virtuelles de base de données principales avec plusieurs cartes réseau.

1. Enregistrez votre script et exécutez-le à partir de l'invite de commandes **PowerShell** ou **PowerShell ISE**. Vous verrez la sortie initiale, comme illustré ci-dessous.

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *                  

		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend

2. Après quelques instants, répondez à l'invite demandant d'indiquer des informations d'identification et cliquez sur **OK**. La sortie ci-dessous représente une seule machine virtuelle. Notez que l’ensemble du processus prend 8 minutes.

		ResourceGroupName            :
		Id                           :
		Name                         : DB2
		Type                         :
		Location                     :
		Tags                         :
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   :
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 :
		InstanceViewText             : null
		NetworkProfile               :
		NetworkProfileText           : null
		OSProfile                    :
		OSProfileText                : null
		Plan                         :
		PlanText                     : null
		ProvisioningState            :
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1}
		NetworkInterfaceIDs          :
		RequestId                    :
		StatusCode                   : 0


		ResourceGroupName            :
		Id                           :
		Name                         : DB2
		Type                         :
		Location                     :
		Tags                         :
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   :
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 :
		InstanceViewText             : null
		NetworkProfile               :
		NetworkProfileText           : null
		OSProfile                    :
		OSProfileText                : null
		Plan                         :
		PlanText                     : null
		ProvisioningState            :
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   },
		                                   {
		                                     "Lun": 1,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-2",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
		NetworkInterfaceIDs          :
		RequestId                    :
		StatusCode                   : 0


		EndTime             : 10/30/2015 9:30:03 AM -08:00
		Error               :
		Output              :
		StartTime           : 10/30/2015 9:22:54 AM -08:00
		Status              : Succeeded
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		StatusCode          : OK

<!---HONumber=AcomDC_0914_2016-->