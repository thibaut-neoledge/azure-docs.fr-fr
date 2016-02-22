<properties
	pageTitle="Mise à jour du script PowerShell d’un projet de groupe de ressources Azure | Microsoft Azure"
	description="Décrit les étapes nécessaires pour mettre à jour manuellement le script PowerShell qui est fourni avec un projet de déploiement de groupe de ressources Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/04/2016"
	ms.author="tarcher" />

# Mise à jour du script PowerShell d’un projet de groupe de ressources Azure

Le déploiement d’un groupe de ressources Azure dans Visual Studio utilise un script Azure PowerShell pour aider à préparer et à déployer vos ressources sur Azure depuis Visual Studio. Version d’Azure PowerShell utilisée par le script dans le projet : 0.9.8. Toutefois, une nouvelle version d’Azure PowerShell, la version 1.0, a été publiée.

La nouvelle version d’Azure PowerShell apporte de nouvelles fonctionnalités et offre une expérience d’utilisation de première classe d’Azure Resource Manager. Toutefois, les modifications apportées à plusieurs fonctions d’applet de commande rendent la version antérieure du script incompatible. Vous pouvez cependant modifier le script pour résoudre ces problèmes, puis exécuter le script en dehors de Visual Studio dans une fenêtre de commande PowerShell. Cet article indique les correctifs à effectuer et leur emplacement. Outre les correctifs spécifiques, une copie du script entièrement modifié est fournie à la fin de cet article.

## Options de contournement

Après l’installation du nouvel Azure PowerShell, des problèmes peuvent se manifester de deux manières différentes dans votre projet de groupe de ressources Azure.

- Étant donné que la boîte de dialogue de déploiement dans Visual Studio ne sera pas en mesure de trouver les nouvelles applets de commande Azure, vous serez invité à les installer. Toutefois, étant donné que les modules ont été renommés dans la nouvelle version des applets de commande, Visual Studio ne sera pas en mesure de trouver les nouveaux modules. Par conséquent, vous pouvez rester bloqué dans une boucle si vous essayez de les installer à partir de Visual Studio.

- Si vous exécutez le script en dehors de Visual Studio dans la fenêtre de commande PowerShell, il est possible que vous obteniez l’erreur suivante :

	*Le terme ’Switch-AzureMode’ n’est pas reconnu comme le nom d’une applet de commande, d’une fonction, d’un fichier de script ou d’un programme exécutable. Vérifiez l’orthographe du nom ou, si un chemin d’accès a été inclus, vérifiez que le chemin d’accès est correct et réessayez.*

Si le nouvel Azure PowerShell vous empêche de déployer votre projet de groupe de ressources Azure, les options suivantes permettent de résoudre le problème.

- Vous pouvez désinstaller la version la plus récente d’Azure PowerShell et réinstaller la version précédente (0.9.8) à l’aide de [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).

- Vous pouvez effectuer des modifications spécifiques ciblées du script PowerShell du projet de déploiement, afin de résoudre les problèmes, puis exécuter ce script manuellement dans la fenêtre de commande PowerShell. Vous ne pourrez pas exécuter le script à partir de l’élément de menu **Déployer** dans Visual Studio. Les étapes de mise à jour du script PowerShell sont répertoriées plus loin dans cet article, de même qu’une copie du script entièrement mis à jour.

## Mettre à jour le script Azure PowerShell
Ces instructions se rapportent aux numéros de ligne. Pour activer la numérotation des lignes dans Visual Studio, consultez [Comment : afficher les numéros de ligne dans l’Éditeur](https://msdn.microsoft.com/library/ms165340.aspx).

1. À la ligne 61, remplacez le code suivant :

	```
	if ($StorageAccountResourceGroupName) {
	        Switch-AzureMode AzureResourceManager
	        $StorageAccountKey = (Get-AzureStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	    }
	    else {
	        Switch-AzureMode AzureServiceManagement
	        $StorageAccountKey = (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary
	    }
	$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
	```

	par :

	```
	$StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	$StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
	```

1. À la ligne 87, remplacez le code suivant :

	```
	$ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission are
	```

	par :

	```
	$ArtifactsLocationSasToken = New-AzureRMStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
	```
1. À la ligne 95, remplacez ce code :

	```
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Force –Verbose
	```

	par le code suivant :

	```
	New-AzureRMResourceGroup `
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment `
		-ResourceGroupName $ResourceGroupName `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-ErrorAction Stop 	

	New-AzureRMResourceGroupDeployment
		-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
	    -ResourceGroupName $ResourceGroupName `
	    -TemplateFile $TemplateFile `
	    -TemplateParameterFile $TemplateParametersFile `
	    @OptionalParameters `
		-Verbose -Force
	```

## Script mis à jour
Voici le script modifié avec toutes les mises à jour mentionnées précédemment effectuées.

```
#Requires -Version 3.0

Param(
  [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
  [string] $ResourceGroupName = '$defaultResourceGroupName$',  
  [switch] $UploadArtifacts,
  [string] $StorageAccountName,
  [string] $StorageAccountResourceGroupName,
  [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
  [string] $TemplateFile = '..\Templates\$deployTemplateFileName$.json',
  [string] $TemplateParametersFile = '..\Templates\$deployTemplateFileName$.parameters.json',
  [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
  [string] $AzCopyPath = '..\Tools\AzCopy.exe',
  [string] $DSCSourceFolder = '..\DSC'
)

Import-Module Azure -ErrorAction SilentlyContinue

try {
  [Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("VSAzureTools-$UI$($host.name)".replace(" ","_"), "2.7.2")
} catch { }

Set-StrictMode -Version 3

$OptionalParameters = New-Object -TypeName Hashtable
$TemplateFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateFile)
$TemplateParametersFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateParametersFile)

if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)

    # Parse the parameter file and update the values of artifacts location and artifacts location SAS token if they are present
    $JsonContent = Get-Content $TemplateParametersFile -Raw | ConvertFrom-Json
    $JsonParameters = $JsonContent | Get-Member -Type NoteProperty | Where-Object {$_.Name -eq "parameters"}

    if ($JsonParameters -eq $null) {
        $JsonParameters = $JsonContent
    }
    else {
        $JsonParameters = $JsonContent.parameters
    }

    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }

    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context

    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }

    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }

    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }

    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
}

# Create or update the resource group using the specified template file and template parameters file
New-AzureRMResourceGroup `
	-Name $ResourceGroupName `
	-Location $ResourceGroupLocation `
	-Verbose -Force -ErrorAction Stop

Test-AzureRmResourceGroupDeployment `
	-ResourceGroupName $ResourceGroupName `
	-TemplateFile $TemplateFile `
	-TemplateParameterFile $TemplateParametersFile `
	@OptionalParameters `
	-ErrorAction Stop 	

New-AzureRMResourceGroupDeployment `
	-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $TemplateFile `
    -TemplateParameterFile $TemplateParametersFile `
    @OptionalParameters `
	-Verbose -Force

```

<!---HONumber=AcomDC_0211_2016-->