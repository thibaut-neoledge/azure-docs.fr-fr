<properties
	pageTitle="Vue d’ensemble du script de déploiement du projet de groupe de ressources Azure | Microsoft Azure"
	description="Décrit le fonctionnement du script PowerShell dans le projet de déploiement du groupe de ressources Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="tfitzmac"
	manager="timlt"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="05/08/2016"
	ms.author="tomfitz" />

# Vue d’ensemble du script de déploiement du projet de groupe de ressources Azure

Les projets de déploiement de groupe de ressources Azure vous aident à effectuer une copie intermédiaire et à déployer des fichiers et d’autres artefacts sur Azure. Lorsque vous créez un projet de déploiement Azure Resource Manager dans Visual Studio, un script PowerShell nommé **Deploy-AzureResourceGroup.ps1** est ajouté au projet. Cette rubrique fournit des détails sur ce que fait ce script et sur la manière de l’exécuter à la fois au sein et en dehors de Visual Studio.

## Que fait le script ?

Le script Deploy-AzureResourceGroup.ps1 effectue deux opérations importantes pour le flux de travail de déploiement.

- Charger tous les fichiers ou artefacts nécessaires au déploiement du modèle
- Déployer le modèle

La première partie du script charge les fichiers et artefacts pour le déploiement, tandis que la dernière applet de commande dans le script déploie le modèle. Par exemple, si une machine virtuelle doit être configurée avec un script, le script de déploiement charge d’abord le script de configuration en toute sécurité sur un compte de stockage Azure. Ainsi, il est disponible pour Azure Resource Manager pour la configuration de la machine virtuelle lors de l’approvisionnement.

Étant donné que tous les déploiements de modèle n’ont pas besoin d’artefacts supplémentaires à charger, un paramètre booléen appelé *uploadArtifacts* est évalué. Si un artefact doit être chargé, définissez le paramètre booléen *uploadArtifacts* lors de l’appel du script. Notez que le fichier de modèle principal et le fichier de paramètres n’ont pas besoin d’être chargés. Seuls d’autres fichiers, tels que les scripts de configuration, les modèles de déploiement imbriqués et les fichiers d’application, doivent être chargés.

## Description détaillée du script

Voici une description des opérations permises par certaines sections du script Azure PowerShell Deploy-AzureResourceGroup.ps1.

>[AZURE.NOTE] Cette section décrit la version 1.0 du script Deploy-AzureResourceGroup.ps1.

1.	Déclarez les paramètres requis par le projet de déploiement Azure Resource Manager. Certains paramètres ont des valeurs par défaut qui ont été définies lors de la création du projet. Vous pouvez modifier ces valeurs par défaut dans le script ou ajouter d’autres valeurs de paramètre avant d’exécuter le script.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

    |Paramètre|Description|
    |---|---|
    |$ResourceGroupLocation|La région ou l’emplacement du centre de données du groupe de ressources, par exemple l’**Ouest des États-Unis** ou l’**Asie de l’Est**.|
    |$ResourceGroupName|Le nom du groupe de ressources Azure.|
    |$UploadArtifacts|Une valeur binaire qui indique si les artefacts doivent être chargés sur Azure à partir de votre système.|
    |$StorageAccountName|Le nom de votre compte de stockage Azure où vos artefacts sont chargés.|
    |$StorageAccountResourceGroupName|Le nom du groupe de ressources Azure qui contient le compte de stockage.|
    |$StorageContainerName|Le nom du conteneur de stockage utilisé pour le chargement des artefacts.|
    |$TemplateFile|Le chemin d’accès vers le fichier de déploiement (`<app name>.json`) dans votre projet de groupe de ressources Azure.|
    |$TemplateParametersFile|Le chemin d’accès vers le fichier de paramètres (`<app name>.parameters.json`) dans votre projet de groupe de ressources Azure.|
    |$ArtifactStagingDirectory|Le chemin d’accès sur votre système où les artefacts sont chargés localement, y compris le dossier racine du script PowerShell. Ce chemin d’accès peut être absolu ou relatif à l’emplacement du script.|
    |$AzCopyPath|Le chemin d’accès où l’outil AzCopy.exe copie ses fichiers .zip, y compris le dossier racine du script PowerShell. Ce chemin d’accès peut être absolu ou relatif à l’emplacement du script.|
    |$DSCSourceFolder|Le chemin d’accès vers le dossier source DSC (configuration d’état souhaité), y compris le dossier racine du script PowerShell. Ce chemin d’accès peut être absolu ou relatif à l’emplacement du script. Consultez la page [Présentation de l’extension Azure PowerShell DSC (configuration d’état souhaité)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx), le cas échéant, pour plus d’informations.|

1.	Vérifiez si les artefacts doivent être chargés sur Azure. Si ce n’est pas le cas, passez à l’étape 11. S’ils doivent être chargés, procédez comme suit.

1.	Convertissez les variables avec des chemins d’accès relatifs en chemins d’accès absolus. Par exemple, modifiez un chemin d’accès tel que `..\Tools\AzCopy.exe` en `C:\YourFolder\Tools\AzCopy.exe`. En outre, initialisez les variables *ArtifactsLocationName* et *ArtifactsLocationSasTokenName* sur la valeur null. *ArtifactsLocation* et *SaSToken* peuvent être des paramètres pour le modèle. Si leurs valeurs sont null après lecture dans le fichier de paramètres, le script génère des valeurs à leur intention.

    Les outils Azure utilisent les valeurs de paramètre *\_artifactsLocation* et *\_artifactsLocationSasToken* dans le modèle pour gérer les artefacts. Si le script PowerShell trouve des paramètres avec ces noms, mais que les valeurs de paramètre ne sont pas fournies, le script charge les artefacts et renvoie les valeurs appropriées pour ces paramètres. Ensuite, il les transmet à l’applet de commande via `@OptionsParameters`.

	|Variable|Description|
    |---|---|
    |ArtifactsLocationName|Le chemin d’accès où se trouvent les artefacts Azure.|
    |ArtifactsLocationSasTokenName|Le nom du jeton SAP (signature d’accès partagé) qui est utilisé par le script pour s’authentifier sur Service Bus. Consultez [Authentification par signature d’accès partagé avec Service Bus](service-bus-shared-access-signature-authentication.md) pour plus d’informations.|

	```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.	Cette section vérifie si le fichier <app name>.parameters.json (également appelé « Fichier de paramètres ») possède un nœud parent nommé **parameters** (dans le bloc `else`). Dans le cas contraire, il n’a pas de nœud parent. Les deux formats sont acceptables.
    
	```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.	Effectuez une itération dans la collection de paramètres JSON. Si une valeur de paramètre a été affectée à *\_artifactsLocation* ou *\_artifactsLocationSasToken*, alors définissez la variable *$OptionalParameters* avec ces valeurs. Cela empêche le script de remplacer par inadvertance les valeurs de paramètre que vous fournissez.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.	Obtenez la clé du compte de stockage et le contexte de la ressource du compte de stockage utilisé pour contenir les artefacts pour le déploiement.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.	Si vous utilisez PowerShell DSC pour configurer une machine virtuelle, l’extension DSC exige que les artefacts se trouvent dans un seul fichier zip. Par conséquent, créez un fichier d’archive .zip pour la configuration DSC. Pour ce faire, vérifiez si $DSCSourceFolder existe. Si une configuration DSC existe, supprimez-la, puis créez un fichier compressé nommé dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.	Si aucun chemin d’accès pour les artefacts Azure n’est fourni dans le fichier de paramètres, définissez un chemin d’accès pour l’utilisation par le script PowerShell lors du chargement des artefacts. Pour ce faire, créez un chemin d’accès à l’aide d’une combinaison associant le chemin d’accès du point de terminaison du compte de stockage et le nom du conteneur de stockage. Ensuite, mettez à jour le fichier de paramètres avec ce nouveau chemin d’accès.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.	Utilisez l’utilitaire **AzCopy** (inclus dans le dossier **Outils** de votre projet de déploiement de groupe de ressources Azure) pour copier tous les fichiers du chemin d’accès de stockage local dans votre compte Azure Storage en ligne. Si cette étape échoue, quittez le script car le déploiement n’est pas susceptible de réussir sans les artefacts requis.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.	Si aucun jeton SAP pour l’emplacement des artefacts n’est fourni dans le fichier de paramètres, créez-en un pour fournir un accès en lecture seule temporaire pour le conteneur de stockage en ligne. Ensuite, transmettez ce jeton SAP à la ligne de commande en tant que paramètre facultatif, « optionalParameter ». Notez que tous les paramètres transmis à la ligne de commande sont prioritaires sur les valeurs fournies dans le fichier de paramètres.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Créez le groupe de ressources s’il n’existe pas déjà, et vérifiez l’absence d’erreurs de validation dans le modèle et le fichier de paramètres qui empêcheraient la réussite du déploiement.

    ```
	# Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Enfin, déployez le modèle. Ce code crée un nom unique pour le déploiement à l’aide d’un horodatage.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## Déployer le groupe de ressources

### Pour déployer le groupe de ressources dans Visual Studio

1. Dans le menu contextuel du projet de groupe de ressources Azure, choisissez **Déployer** > **Nouveau déploiement**.

    ![][0]

1. Dans la boîte de dialogue **Déployer vers le groupe de ressources**, choisissez un groupe de ressources existant à déployer dans la liste déroulante ou choisissez **&lt;Créer…&gt;** pour créer un groupe de ressources.

    ![][1]

1. Si vous y êtes invité, entrez un nom et un emplacement pour le groupe de ressources dans la boîte de dialogue **Créer un groupe de ressources**, puis choisissez le bouton **Créer**.

    ![][2]

1. Choisissez le bouton **Modifier les paramètres** pour afficher la boîte de dialogue **Modifier les paramètres**, puis entrez les valeurs de paramètre manquantes.

    ![][3]

	>[AZURE.NOTE] Si un paramètre requis a besoin de valeurs, cette boîte de dialogue s’affiche automatiquement lors du déploiement.

    ![][4]

1. Lorsque vous avez terminé d’entrer les valeurs de paramètre, choisissez le bouton **Enregistrer**, puis le bouton **Déployer**.

    Le script de déploiement (Deploy-AzureResourceGroup.ps1) s’exécute et votre modèle (ainsi que tous les artefacts) se déploie sur Azure.

### Pour déployer le groupe de ressources à l’aide de PowerShell

Si vous souhaitez exécuter le script sans utiliser les commandes et l’interface utilisateur de déploiement Visual Studio, dans le menu contextuel pour le script, choisissez **Ouvrir avec PowerShell ISE**.

![][5]


## Exemples de déploiements de commandes

### Déployer à l’aide des valeurs par défaut

Cet exemple montre comment exécuter le script en utilisant les valeurs de paramètre par défaut. (Étant donné que le paramètre d’emplacement n’a pas de valeur par défaut, vous devez en fournir une.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### Déployer en remplaçant les valeurs par défaut

Cet exemple montre comment exécuter le script pour déployer le modèle et les fichiers de paramètres qui diffèrent des valeurs par défaut.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### Déployer à l’aide d’UploadArtifacts pour la copie intermédiaire

Cet exemple montre comment exécuter le script pour charger des artefacts à partir du dossier de version et déployer les modèles autres que par défaut.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

Cet exemple montre comment exécuter le script dans une tâche Azure PowerShell dans Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## Étapes suivantes
En savoir plus sur Azure Resource Manager en consultant [Présentation d’Azure Resource Manager](resource-group-overview.md).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png

<!---HONumber=AcomDC_0713_2016-->