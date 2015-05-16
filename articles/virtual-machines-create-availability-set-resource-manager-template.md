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
	ms.date="04/20/2015" 
	ms.author="kathydav"/>

# Création d’un groupe à haute disponibilité à l’aide de modèles d’Azure Resource Manager

Vous pouvez facilement créer un groupe à haute disponibilité pour une machine virtuelle à l’aide d’Azure PowerShell ou de l’interface de ligne de commande interplateforme \(xplat-cli\) et d’un modèle du Gestionnaire de ressources. Ce modèle crée un groupe à haute disponibilité.
 
Avant d’aller plus loin, veuillez vérifier qu’Azure PowerShell et l’interface de ligne de commande interplateforme sont configurés et opérationnels.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../includes/xplat-getting-set-up.md)]


## [utilisez] un modèle du Gestionnaire de ressources avec Azure PowerShell

Procédez comme suit pour [utiliser] un modèle du Gestionnaire de ressources dans le référentiel de modèles Github avec Azure PowerShell.

### Étape 1 : téléchargement du fichier JSON

Définissez un dossier local comme emplacement pour les fichiers du modèle JSON puis créez-le \(par exemple, C:\\Azure\\Templates\[thing\]\).

Remplacez le nom du dossier, puis copiez et exécutez ces commandes.

	$folderName="<folder name, such as C:\Azure\Templates\[thing]>"
	$webclient = New-Object System.Net.WebClient
	$url = "[Writers: add the URL to the RAW version of the target template in GitHub]"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### Étape 2 : \(facultative\) affichage des paramètres

Lorsque vous [utilisez] un modèle, vous devez spécifier un ensemble de paramètres de configuration. Pour afficher les paramètres que vous devez spécifier pour le modèle dans un fichier JSON local avant d’exécuter la commande pour créer la machine virtuelle, ouvrez le fichier JSON dans un outil ou un éditeur de texte de votre choix. Recherchez la section "parameters" située en haut du fichier, qui répertorie l’ensemble des paramètres requis par le modèle pour configurer la machine virtuelle. Voici la section **"parameters"** du modèle azuredeploy.json :

[Remarque pour les rédacteurs : collez la section "parameters" du modèle azuredeploy.json et procédez à sa mise en forme en respectant le format du code.]

### Étape 3 : récupération des [informations nécessaires pour terminer le modèle]

[Remarque pour les rédacteurs : pensez à mettre en place une section facultative pour collecter les valeurs de paramètre si nécessaire.]

### Étape 4 : [utilisation] du modèle

Entrez un nom de déploiement Azure, un nom de groupe de ressources, un emplacement Azure, le dossier où se trouve votre fichier JSON enregistré, puis exécutez les commandes suivantes.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\[thing]>" 
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Lorsque vous exécutez la commande  **New-AzureResourceGroupDeployment**, vous êtes invité à entrer les valeurs des paramètres dans la section **"parameters"** du fichier JSON. Une fois cette opération effectuée, la commande crée le groupe de ressources et le groupe à haute disponibilité.

Voici un exemple de la commande PowerShell définie pour le modèle.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates\[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Le résultat suivant doit s’afficher :

[Remarque pour les rédacteurs : collez les valeurs affichées par PowerShell pour les premiers paramètres d’invite, en remplaçant ceci :]

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Pour supprimer ce groupe de ressources et l’ensemble de ces ressources \(le compte de stockage, la machine virtuelle et le réseau virtuel\), utilisez la commande suivante.

	Remove-AzureResourceGroup –Name "<resource group name>"


## [utilisez] un modèle du Gestionnaire de ressources avec l’interface de ligne de commande interplateforme \(xplat-cli\)

Procédez comme suit pour [utiliser] un modèle du Gestionnaire de ressources dans le référentiel de modèles Github avec les commandes de l’interface de ligne de commande interplateforme.

### Étape 1 : téléchargement du fichier JSON du modèle

Définissez un dossier local comme emplacement pour les fichiers du modèle JSON puis créez-le \(par exemple, C:\\Azure\\Templates\[thing\]\).

Indiquez le nom du dossier, puis exécutez les commandes suivantes.

[commandes de l’interface de ligne de commande interplateforme pour télécharger le fichier de modèle]

### Étape 2 : \(facultative\) affichage des paramètres du modèle

Lorsque vous [utilisez] un modèle, vous devez spécifier un ensemble de paramètres de configuration. Pour afficher les paramètres que vous devez spécifier pour le modèle dans un fichier JSON local avant d’exécuter la commande pour créer la machine virtuelle, ouvrez le fichier JSON dans un outil ou un éditeur de texte de votre choix. Recherchez la section "parameters" située en haut du fichier, qui répertorie l’ensemble des paramètres requis par le modèle pour configurer la machine virtuelle. Voici la section **"parameters"** du modèle azuredeploy.json :

[Remarque pour les rédacteurs : collez la section "parameters" du modèle azuredeploy.json et procédez à sa mise en forme en respectant le format du code.]

### Étape 3 : récupération des [informations nécessaires pour terminer le modèle]

[Remarque pour les rédacteurs : pensez à mettre en place une section facultative pour collecter les valeurs de paramètre si nécessaire.]

### Étape 4 : [utilisation] du modèle

Renseignez le champ \[informations requises}, puis exécutez ces commandes.

[commandes de l’interface de ligne de commande interplateforme pour exécuter le fichier de modèle]

[explication sur la méthode utilisée par l’interface de ligne de commande interplateforme pour exécuter le modèle]


Voici un exemple de la commande de l’interface de ligne de commande interplateforme définie pour le modèle.

[exemple de commande de l’interface de ligne de commande interplateforme]

Le résultat suivant doit s’afficher :

[Remarque pour les rédacteurs : collez l’affichage de l’interface de ligne de commande pour les premières invites de paramètres]


Pour supprimer ce groupe de ressources et l’ensemble de ces ressources \([éléments du groupe de ressources]\), utilisez cette commande.

[commande de l’interface de ligne de commande interplateforme]




<!--HONumber=52-->
