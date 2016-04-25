<properties
   pageTitle="Déploiement de machines virtuelles à plusieurs cartes réseau à l'aide de l'Interface de ligne de commande (CLI) Azure dans Resource Manager | Microsoft Azure"
   description="Apprenez à déployer des machines virtuelles à plusieurs cartes réseau à l'aide de l'Interface de ligne de commande Azure dans Resource Manager."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
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
   ms.author="telmos" />

#Déploiement de plusieurs machines virtuelles à cartes réseau (classiques) à l'aide de l'interface de ligne de commande d’Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Actuellement, un même groupe de ressources ne peut pas avoir des machines virtuelles avec une seule carte réseau et des machines virtuelles avec plusieurs cartes réseau. Par conséquent, vous devez implémenter les serveurs principaux dans un groupe de ressources différent de celui des autres composants. Les étapes ci-dessous utilisent un groupe de ressources nommé *IaaSStory* pour le groupe de ressources principal et *IaaSStory-BackEnd* pour les serveurs principaux.

## Composants requis

Avant de déployer les serveurs principaux, vous devez déployer le groupe de ressources principal avec toutes les ressources nécessaires pour ce scénario. Pour déployer ces ressources, procédez comme suit.

1. Accédez à la [page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Dans la page de modèle, à droite du **groupe de ressources parent**, cliquez sur **Déployer dans Azure**.
3. Si nécessaire, modifiez les valeurs de paramètre, puis suivez les étapes du portail Azure en version préliminaire pour déployer le groupe de ressources.

> [AZURE.IMPORTANT] Assurez-vous que vos noms de compte de stockage sont uniques. Vous ne pouvez avoir des noms de compte de stockage en double dans Azure.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Déploiement des machines virtuelles principales

Les machines virtuelles principales dépendent de la création de ressources répertoriées ci-dessous.

- **Compte de stockage pour les disques de données**. Pour optimiser les performances, les disques de données sur les serveurs de base de données utilisent la technologie de disque SSD, qui requiert un compte de stockage Premium. Assurez-vous que l’emplacement Azure de déploiement prend en charge le stockage Premium.
- **Cartes réseau**. Chaque machine virtuelle a deux cartes réseau, une pour l’accès à la base de données et l’autre pour la gestion.
- **Groupe à haute disponibilité**. Tous les serveurs de base de données sont ajoutés à un groupe à haute disponibilité, afin de garantir qu’au moins une des machines virtuelles est en cours d’exécution lors de la maintenance.

### Étape 1 : démarrage de votre script

Vous pouvez télécharger le script d'interpréteur de commandes complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-cli.sh). Suivez les étapes ci-dessous pour modifier le script afin qu’il fonctionne dans votre environnement.

1. Modifiez les valeurs des variables suivantes selon votre groupe de ressources existant déployé ci-dessus dans les [Conditions préalables](#Prerequisites).

		existingRGName="IaaSStory"
		location="westus"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"
		remoteAccessNSGName="NSG-RemoteAccess"

2. Modifiez les valeurs des variables suivantes, selon les valeurs que vous souhaitez utiliser pour le déploiement de votre serveur principal.

		backendRGName="IaaSStory-Backend"
		prmStorageAccountName="wtestvnetstorageprm"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		publisher="Canonical"
		offer="UbuntuServer"
		sku="14.04.2-LTS"
		version="latest"
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskName="datadisk"
		nicNamePrefix="NICDB"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

3. Récupérez l'ID du sous-réseau `BackEnd` où les machines virtuelles seront créés. Vous devez le faire dans la mesure où les cartes à associer à ce sous-réseau se trouvent dans un autre groupe de ressources.

		subnetId="$(azure network vnet subnet show --resource-group $existingRGName \
		                --vnet-name $vnetName \
		                --name $backendSubnetName|grep Id)"
		subnetId=${subnetId#*/}

>[AZURE.TIP] La première commande ci-dessus utilise [grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html) et [la manipulation des chaînes](http://tldp.org/LDP/abs/html/string-manipulation.html) (plus précisément, suppression de sous-chaîne).

4. Récupérez l'ID du groupe de sécurité réseau `NSG-RemoteAccess`. Vous devez le faire dans la mesure où les cartes à associer à ce groupe de sécurité réseau se trouvent dans un autre groupe de ressources.

		nsgId="$(azure network nsg show --resource-group $existingRGName \
		                --name $remoteAccessNSGName|grep Id)"
		nsgId=${nsgId#*/}

### Étape 2 : création des ressources nécessaires pour vos machines virtuelles

1. Créez un groupe de ressources pour toutes les ressources de serveur principal. Notez l'utilisation de la variable `$backendRGName` pour le nom du groupe de ressources, et `$location` pour la région Azure.

		azure group create $backendRGName $location

2. Créez un compte de stockage Premium pour les disques du système d'exploitation et les disques de données à utiliser par vos machines virtuelles.

		azure storage account create $prmStorageAccountName \
		    --resource-group $backendRGName \
		    --location $location \
			--type PLRS

3. Créez un groupe à haute disponibilité pour les machines virtuelles

		azure availset create --resource-group $backendRGName \
		    --location $location \
		    --name $avSetName

### Étape 3: création des cartes réseau et des machines virtuelles principales

1. Lancez une boucle pour créer plusieurs machines virtuelles, selon les variables `numberOfVMs`.

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. Pour chaque machine virtuelle, créez une carte réseau pour l'accès de la base de données.

		    nic1Name=$nicNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x
		    azure network nic create --name $nic1Name \
		        --resource-group $backendRGName \
		        --location $location \
		        --private-ip-address $ipAddress1 \
		        --subnet-id $subnetId

3. Pour chaque machine virtuelle, créez une carte réseau pour l'accès à distance. Notez le paramètre `--network-security-group`, utilisé pour associer la carte réseau à un groupe de sécurité réseau.

		    nic2Name=$nicNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x
		    azure network nic create --name $nic2Name \
		        --resource-group $backendRGName \
		        --location $location \
		        --private-ip-address $ipAddress2 \
		        --subnet-id $subnetId $vnetName \
		        --network-security-group-id $nsgId

4. Créez la machine virtuelle.

		    azure vm create --resource-group $backendRGName \
		        --name $vmNamePrefix$suffixNumber \
		        --location $location \
		        --vm-size $vmSize \
		        --subnet-id $subnetId \
		        --availset-name $avSetName \
		        --nic-names $nic1Name,$nic2Name \
		        --os-type linux \
		        --image-urn $publisher:$offer:$sku:$version \
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --os-disk-vhd $osDiskName$suffixNumber.vhd \
		        --admin-username $username \
		        --admin-password $password

5. Pour chaque machine virtuelle, créez deux disques de données et terminez la boucle avec la commande `done`.

		    azure vm disk attach-new --resource-group $backendRGName \
		        --vm-name $vmNamePrefix$suffixNumber \        
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --vhd-name $dataDiskName$suffixNumber-1.vhd \
		        --size-in-gb $diskSize \
		        --lun 0

		    azure vm disk attach-new --resource-group $backendRGName \
		        --vm-name $vmNamePrefix$suffixNumber \        
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --vhd-name $dataDiskName$suffixNumber-2.vhd \
		        --size-in-gb $diskSize \
		        --lun 1
		done


### Étape 4 : exécution du script

Maintenant que vous avez téléchargé et modifié le script selon vos besoins, exécutez le script pour créer les machines virtuelles principales de base de données avec plusieurs cartes réseau.

1. Enregistrez votre script et exécutez-le à partir de votre terminal **Bash**. Vous verrez la sortie initiale, comme illustré ci-dessous.

		info:    Executing command group create
		info:    Getting resource group IaaSStory-Backend
		info:    Creating resource group IaaSStory-Backend
		info:    Created resource group IaaSStory-Backend
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
		data:    Name:                IaaSStory-Backend
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command availset create
		info:    Looking up the availability set "ASDB"
		info:    Creating availability set "ASDB"
		info:    availset create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB1-DA"
		info:    Creating network interface "NICDB1-DA"
		info:    Looking up the network interface "NICDB1-DA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-DA
		data:    Name                            : NICDB1-DA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB1-RA"
		info:    Creating network interface "NICDB1-RA"
		info:    Looking up the network interface "NICDB1-RA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-RA
		data:    Name                            : NICDB1-RA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.54
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command vm create
		info:    Looking up the VM "DB1"
		info:    Using the VM Size "Standard_DS3"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account wtestvnetstorageprm
		info:    Looking up the availability set "ASDB"
		info:    Found an Availability set "ASDB"
		info:    Looking up the NIC "NICDB1-DA"
		info:    Looking up the NIC "NICDB1-RA"
		info:    Creating VM "DB1"

2. Après quelques minutes, l'exécution s'arrête et le reste de la sortie s'affiche, comme illustré ci-dessous.

		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB1"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-1.vhd
		info:    Updating VM "DB1"
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB1"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-2.vhd
		info:    Updating VM "DB1"
		info:    vm disk attach-new command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB2-DA"
		info:    Creating network interface "NICDB2-DA"
		info:    Looking up the network interface "NICDB2-DA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-DA
		data:    Name                            : NICDB2-DA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.5
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB2-RA"
		info:    Creating network interface "NICDB2-RA"
		info:    Looking up the network interface "NICDB2-RA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-RA
		data:    Name                            : NICDB2-RA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.55
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command vm create
		info:    Looking up the VM "DB2"
		info:    Using the VM Size "Standard_DS3"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account wtestvnetstorageprm
		info:    Looking up the availability set "ASDB"
		info:    Found an Availability set "ASDB"
		info:    Looking up the NIC "NICDB2-DA"
		info:    Looking up the NIC "NICDB2-RA"
		info:    Creating VM "DB2"
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB2"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-1.vhd
		info:    Updating VM "DB2"
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB2"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-2.vhd
		info:    Updating VM "DB2"
		info:    vm disk attach-new command OK

<!---HONumber=AcomDC_0413_2016-->