<properties
   pageTitle="Déploiement de machines virtuelles à plusieurs cartes réseau à l'aide de l'Interface de ligne de commande (CLI) Azure dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à déployer des machines virtuelles à plusieurs cartes réseau à l'aide de l'Interface de ligne de commande Azure dans le modèle de déploiement classique"
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
   ms.date="02/02/2016"
   ms.author="telmos" />

#Déploiement de plusieurs machines virtuelles à cartes réseau (classiques) à l'aide de l'interface de ligne de commande d’Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Actuellement, dans un même service cloud, vous ne pouvez pas avoir des machines virtuelles avec une seule carte réseau et des machines virtuelles avec plusieurs cartes réseau. Par conséquent, vous devez implémenter les serveurs principaux dans un service cloud différent de celui des autres composants du scénario. Les étapes ci-dessous utilisent un service cloud nommé *IaaSStory* pour les ressources principales et *IaaSStory-BackEnd* pour les serveurs principaux.

## Composants requis

Avant de déployer les serveurs principaux, vous devez déployer le service de cloud principal avec toutes les ressources nécessaires pour ce scénario. Au minimum, vous devez créer un réseau virtuel avec un sous-réseau pour le serveur principal. Consultez [Créer un réseau virtuel (classique) à l'aide de l'interface de ligne de commande Azure](virtual-networks-create-vnet-classic-cli.md) pour apprendre à déployer un réseau virtuel.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Déploiement des machines virtuelles principales

Les machines virtuelles principales dépendent de la création de ressources répertoriées ci-dessous.

- **Compte de stockage pour les disques de données**. Pour optimiser les performances, les disques de données sur les serveurs de base de données utilisent la technologie de disque SSD, qui requiert un compte de stockage Premium. Assurez-vous que l’emplacement Azure de déploiement prend en charge le stockage Premium.
- **Cartes réseau**. Chaque machine virtuelle a deux cartes réseau, une pour l’accès à la base de données et l’autre pour la gestion.
- **Groupe à haute disponibilité**. Tous les serveurs de base de données sont ajoutés à un groupe à haute disponibilité, afin de garantir qu’au moins une des machines virtuelles est en cours d’exécution lors de la maintenance.

### Étape 1 : démarrage de votre script

Vous pouvez télécharger le script d'interpréteur de commandes complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Suivez les étapes ci-dessous pour modifier le script afin qu’il fonctionne dans votre environnement.

1. Modifiez les valeurs des variables suivantes selon votre groupe de ressources existant déployé ci-dessus dans les [Conditions préalables](#Prerequisites).

		location="useast2"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"

2. Modifiez les valeurs des variables suivantes, selon les valeurs que vous souhaitez utiliser pour le déploiement de votre serveur principal.

		backendCSName="IaaSStory-Backend"
		prmStorageAccountName="iaasstoryprmstorage"
		image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskPrefix="db"
		dataDiskName="datadisk"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

### Étape 2 : création des ressources nécessaires pour vos machines virtuelles

1. Créez un service cloud pour toutes les machines virtuelles principales. Notez l'utilisation de la variable `$backendCSName` pour le nom du groupe de ressources, et `$location` pour la région Azure.

		azure service create --serviceName $backendCSName \
		    --location $location

2. Créez un compte de stockage Premium pour les disques du système d'exploitation et les disques de données à utiliser par vos machines virtuelles.

		azure storage account create $prmStorageAccountName \
		    --location $location \
		    --type PLRS

### Étape 3 : création de machines virtuelles avec plusieurs cartes d’interface réseau

1. Lancez une boucle pour créer plusieurs machines virtuelles, selon les variables `numberOfVMs`.

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. Pour chaque machine virtuelle, spécifiez le nom et l'adresse IP de chacune des deux cartes réseau.

		    nic1Name=$vmNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x

		    nic2Name=$vmNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x

4. Créez la machine virtuelle. Notez l'utilisation du paramètre `--nic-config` contenant une liste de toutes les cartes réseau avec adresse IP, sous-réseau et nom.

		    azure vm create $backendCSName $image $username $password \
		        --connect $backendCSName \
		        --vm-name $vmNamePrefix$suffixNumber \
		        --vm-size $vmSize \
		        --availability-set $avSetName \
		        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
		        --virtual-network-name $vnetName \
		        --subnet-names $backendSubnetName \
		        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Pour chaque machine virtuelle, créez deux disques de données.

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
		done

### Étape 4 : exécution du script

Maintenant que vous avez téléchargé et modifié le script selon vos besoins, exécutez le script pour créer les machines virtuelles principales de base de données avec plusieurs cartes réseau.

1. Enregistrez votre script et exécutez-le à partir de votre terminal **Bash**. Vous verrez la sortie initiale, comme illustré ci-dessous.

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name IaaSStory-Backend
		info:    service create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM

2. Après quelques minutes, l'exécution s'arrête et le reste de la sortie s'affiche, comme illustré ci-dessous.

		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM
		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK

<!---HONumber=AcomDC_0413_2016-->