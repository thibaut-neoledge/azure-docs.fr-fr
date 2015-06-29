<properties 
	pageTitle="Création d’un groupe à haute disponibilité à l’aide de modèles d’Azure Resource Manager" 
	description="Explique comment utiliser le modèle du groupe à haute disponibilité et présente la syntaxe du modèle" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="kathydav"/>

# Création d’un groupe à haute disponibilité à l’aide de modèles d’Azure Resource Manager

Vous pouvez facilement créer un groupe à haute disponibilité pour une machine virtuelle à l’aide de Microsoft Azure PowerShell ou de l’interface de ligne de commande Microsoft Azure et d’un modèle Microsoft Azure Manager. Ce modèle crée un groupe à haute disponibilité.
 
Avant d’aller plus loin, veuillez vérifier que Microsoft Azure PowerShell et l’interface de ligne de commande Microsoft Azure sont configurés et opérationnels.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../../includes/xplat-getting-set-up.md)]


## Créer un groupe à haute disponibilité à l’aide du modèle Microsoft Azure Resource Manager

Procédez comme suit pour créer un groupe à haute disponibilité pour une machine virtuelle en utilisant un modèle de Microsoft Azure Resource Manager dans le référentiel de modèles Github avec Microsoft Azure PowerShell.

### Étape 1 : téléchargement du fichier JSON

Définissez un dossier local comme emplacement pour les fichiers du modèle JSON puis créez-le (par exemple, C:\Azure\\Templates\\availability).

Remplacez le nom du dossier, puis copiez et exécutez ces commandes.

	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### Étape 2 : Collecter les détails des paramètres requis

Lorsque vous utilisez un modèle, vous devez communiquer des détails comme l’emplacement, le nom de définition, etc. Pour connaître les paramètres requis pour un modèle, effectuez l’une des opérations suivantes :

- Examinez [ici](http://azure.microsoft.com/documentation/templates/201-2-vms-2-FDs-no-resource-loops/) la liste des paramètres.
- Ouvrez le fichier JSON dans un outil ou un éditeur de texte de votre choix. Recherchez la section "parameters" située en haut du fichier, qui répertorie l’ensemble des paramètres requis par le modèle pour configurer la machine virtuelle. 

Collectez les informations à saisir. Lorsque vous exécutez la commande de déploiement du modèle, vous serez invité à saisir les informations.

### Étape 3 : Créer le groupe à haute disponibilité

Les sections suivantes vous expliquent comment utiliser Microsoft Azure PowerShell ou l’interface de ligne de commande Microsoft Azure à cette fin.

### Utilisation d'Azure PowerShell

Entrez un nom de déploiement Azure, un nom de groupe de ressources, un emplacement Azure, le dossier où se trouve votre fichier JSON enregistré, puis exécutez les commandes suivantes.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\availability>" 
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Lorsque vous exécutez la commande **New-AzureResourceGroupDeployment**, vous êtes invité à entrer les valeurs des paramètres dans la section **"parameters"** du fichier JSON. Une fois cette opération effectuée, la commande crée le groupe de ressources et le groupe à haute disponibilité.

Voici un exemple de la commande PowerShell définie pour le modèle.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Le résultat suivant devrait s’afficher :

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Pour supprimer ce groupe de ressources et l’ensemble de ces ressources (le compte de stockage, la machine virtuelle et le réseau virtuel), utilisez la commande suivante.

	Remove-AzureResourceGroup –Name "<resource group name>"


## Utiliser l’interface de ligne de commande Microsoft Azure

Procédez comme suit pour créer le groupe à haute disponibilité en utilisant un modèle de Microsoft Azure Resource Manager dans le référentiel de modèles Github avec une commande de l’interface de ligne de commande Microsoft Azure.

	azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json





 

<!---HONumber=58_postMigration-->