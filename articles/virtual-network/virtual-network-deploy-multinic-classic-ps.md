<properties 
   pageTitle="Déploiement de machines virtuelles à plusieurs cartes réseau à l'aide de PowerShell dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à déployer des machines virtuelles à plusieurs cartes réseau à l'aide de PowerShell dans le modèle de déploiement classique"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="telmos" />

#Déploiement de plusieurs machines virtuelles à cartes réseau (classiques) à l'aide de PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Étant donné que vous ne pouvez pas pour l’instant avoir des machines virtuelles avec une seule carte réseau et des machines virtuelles avec plusieurs cartes réseau dans le même service cloud, vous allez implémenter les serveurs principaux dans un service cloud différent, ainsi que tous les autres composants dans le scénario. Les étapes ci-dessous utilisent un service cloud nommé *IaaSStory* pour les ressources principales et *IaaSStory-BackEnd* pour les serveurs principaux.

## Composants requis

Avant de déployer les serveurs principaux, vous devez déployer le service de cloud principal avec toutes les ressources nécessaires pour ce scénario. Au minimum, vous devez créer un réseau virtuel avec un sous-réseau pour le serveur principal. Consultez [Créer un réseau virtuel (classique) à l'aide de PowerShell](virtual-networks-create-vnet-classic-ps.md) pour apprendre à déployer un réseau virtuel.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Déploiement des machines virtuelles principales

Les machines virtuelles principales dépendent de la création de ressources répertoriées ci-dessous.

- **Sous-réseau principal**. Les serveurs de base de données font partie d'un sous-réseau distinct, pour répartir le trafic. Le script ci-dessous s'attend à ce que le sous-réseau se trouve dans un réseau virtuel nommé *WTestVnet*.
- **Compte de stockage pour les disques de données**. Pour optimiser les performances, les disques de données sur les serveurs de base de données utilisent la technologie de disque SSD, qui requiert un compte de stockage Premium. Assurez-vous que l'emplacement Azure de déploiement prend en charge le stockage Premium.
- **Groupe à haute disponibilité**. Tous les serveurs de base de données sont ajoutés à un groupe à haute disponibilité, afin de garantir qu’au moins une des machines virtuelles est en cours d'exécution lors de la maintenance. 

### Étape 1 : démarrage de votre script

Vous pouvez télécharger le script PowerShell complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/multinic.ps1). Suivez les étapes ci-dessous pour modifier le script afin qu’il fonctionne dans votre environnement.

1. Modifiez les valeurs des variables suivantes selon votre groupe de ressources existant déployé ci-dessus dans les [Conditions préalables](#Prerequisites).

		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"

2. Modifiez les valeurs des variables suivantes, selon les valeurs que vous souhaitez utiliser pour votre déploiement de serveur principal.

		$backendCSName         = "IaaSStory-Backend"
		$prmStorageAccountName = "iaasstoryprmstorage"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$diskSize              = 127
		$vmNamePrefix          = "DB"
		$dataDiskSuffix        = "datadisk"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

### Étape 2 : création des ressources nécessaires pour vos machines virtuelles

Vous devez créer un nouveau service cloud et un compte de stockage pour les disques de données pour toutes les machines virtuelles. Vous devez également spécifier une image et un compte d'administrateur local pour les machines virtuelles. Pour créer ces ressources, exécutez les étapes suivantes.

1. Créez un nouveau service cloud.

		New-AzureService -ServiceName $backendCSName -Location $location

2. Créez un compte de stockage Premium.

		New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
		    -Location $location `
		    -Type Premium_LRS

3. Définissez le compte de stockage créé ci-dessus en tant que compte de stockage actif pour votre abonnement.

		$subscription = Get-AzureSubscription `
		    | where {$_.IsCurrent -eq $true}  
		Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
		    -CurrentStorageAccountName $prmStorageAccountName

4. Sélectionnez une image de machine virtuelle.

		$image = Get-AzureVMImage `
		    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
		    | sort PublishedDate -Descending `
		    | select -ExpandProperty ImageName -First 1

5. Définissez les informations d’identification de compte d’administrateur local.

		$cred = Get-Credential -Message "Enter username and password for local admin account"

### Étape 3 : création de machines virtuelles

Vous devez utiliser une boucle pour créer le nombre de machines virtuelles que vous voulez et créer les cartes réseau et les machines virtuelles nécessaires au sein de la boucle. Pour créer ces cartes réseau et machines virtuelles, exécutez les étapes suivantes.

1. Lancez une boucle `for` pour répéter les commandes afin de créer une machine virtuelle et deux cartes réseau, autant de fois que nécessaire, selon la valeur de la variable `$numberOfVMs`.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Créez un objet `VMConfig` spécifiant l'image, la taille et la haute disponibilité de la machine virtuelle.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureVMConfig -Name $vmName `
		                    -ImageName $image `
		                    -InstanceSize $vmSize `
		                    -AvailabilitySetName $avSetName  

3. Configurez la machine virtuelle en tant que machine virtuelle Windows.

		    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
		        -AdminUsername $cred.UserName `
		        -Password $cred.Password

4. Définissez la carte réseau par défaut et attribuez-lui une adresse IP statique.

		    Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
		    Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Ajoutez une deuxième carte réseau pour chaque machine virtuelle.

		    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
		        -SubnetName $backendSubnetName `
		        -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
		        -VM $vmConfig 

6. Pour chaque machine virtuelle, créez deux disques de données.

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk1Name `
		        -LUN 0       
		
		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk2Name `
		        -LUN 1

7. Créez toutes les machines virtuelles et mettez fin à la boucle.

		    New-AzureVM -VM $vmConfig `
		        -ServiceName $backendCSName `
		        -Location $location `
		        -VNetName $vnetName
		}

### Étape 4 : exécution du script

Maintenant que vous avez téléchargé et modifié le script selon vos besoins, exécutez le script pour créer les machines virtuelles principales de base de données avec plusieurs cartes réseau.

1. Enregistrez votre script et exécutez-le à partir de l’invite de commandes **PowerShell** ou **PowerShell ISE**. Vous verrez la sortie initiale, comme illustré ci-dessous.

		OperationDescription    OperationId                          OperationStatus
		--------------------    -----------                          ---------------
		New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		                                                                            
		WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Complétez les informations nécessaires dans l'invite d'informations d'identification et cliquez sur **OK**. Le résultat ci-dessous s'affiche.

		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded 

<!---HONumber=Nov15_HO4-->