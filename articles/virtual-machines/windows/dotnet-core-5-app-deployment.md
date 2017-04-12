---
title: "Automatisation du déploiement d’application avec des extensions de machine virtuelle | Microsoft Docs"
description: Didacticiel sur DotNet Core pour les machines virtuelles Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 79c91304-6c1b-4354-a185-fecc129b139d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 2d1860a16405646452f6448c26bf995473ac04b3
ms.lasthandoff: 03/31/2017


---
# <a name="application-deployment-with-azure-resource-manager-templates-for-windows-vms"></a>Déploiement d’applications avec des modèles Azure Resource Manager pour les machines virtuelles Windows

Une fois que toutes les exigences infrastructurelles d’Azure sont identifiées et converties en un modèle de déploiement, le déploiement de l’application réelle doit être effectué. Le déploiement de l’application fait ici référence à l’installation des fichiers binaires de l’application réelle sur les ressources Azure. Pour l’exemple du Store musique, .NET Core et IIS doivent être installés et configurés sur chaque machine virtuelle. Les fichiers binaires du Store musique doivent être installés sur la machine virtuelle, et la base de données du Store musique doit avoir été créée au préalable.

Ce document décrit en détail comment des extensions de machine virtuelle peuvent automatiser le déploiement et la configuration d’applications sur des machines virtuelles Azure. Toutes les dépendances et configurations uniques sont en surbrillance. Pour optimiser l’expérience, prédéployez une instance de la solution sur votre abonnement Azure et travaillez avec le modèle Azure Resource Manager. Pour accéder au modèle complet, consultez [Déploiement du Store musique sous Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="configuration-script"></a>Script de configuration
Les extensions de machine virtuelle sont des programmes spécialisés qui s’exécutent sur des machines virtuelles pour automatiser la configuration. Les extensions sont disponibles pour de nombreux objectifs spécifiques tels que la protection contre les virus, la configuration de la journalisation et la configuration de Docker. L’extension de script personnalisé peut être utilisée pour exécuter un script sur une machine virtuelle. Avec l’exemple du Store musique, l’extension de script personnalisé doit configurer les machines virtuelles Windows et installer l’application du Store musique.

Avant de détailler la manière dont les extensions de machine virtuelle sont déclarées dans un modèle Azure Resource Manager, examinez le script exécuté. Celui-ci configure la machine virtuelle Windows pour héberger l’application du Store musique. Lors de son exécution, le script installe tous les logiciels nécessaires, installe l’application du Store musique à partir du contrôle de code source et prépare la base de données. 

> Cet exemple ne sert qu’à des fins de démonstration.

```PowerShell
<#
    .SYNOPSIS
        Downloads and configures .Net Core Music Store application sample across IIS and Azure SQL DB.
#>

Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# Firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# Folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# Install iis
Install-WindowsFeature web-server -IncludeManagementTools

# Install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# Download music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music

# Azure SQL connection sting in environment variable
[Environment]::SetEnvironmentVariable("Data:DefaultConnection:ConnectionString", "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30",[EnvironmentVariableTarget]::Machine)

# Pre-create database
$env:Data:DefaultConnection:ConnectionString = "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30"
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# Configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Extension de script de machine virtuelle
Les extensions de machine virtuelle peuvent être exécutées sur une machine virtuelle au moment de sa génération en incluant la ressource d’extension dans le modèle Azure Resource Manager. L’extension peut être ajoutée avec l’Assistant Ajouter une ressource de Visual Studio, ou en insérant un JSON valide dans le modèle. La ressource d’extension de script est imbriquée dans la ressource de machine virtuelle, comme le montre l’exemple suivant.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Extension de script de machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Notez que, dans le JSON ci-dessous, le script est stocké dans GitHub. Ce script pourrait également être stocké dans Stockage Blob Azure. De même, les modèles Azure Resource Manager permettent de construire la chaîne d’exécution du script de façon à ce que les valeurs des paramètres du modèle puissent être utilisées en tant que paramètres pour l’exécution du script. Dans ce cas, des données sont fournies lors du déploiement des modèles et ces valeurs peuvent ensuite être utilisées lors de l’exécution du script.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Comme mentionné ci-dessus, il est également possible de stocker vos scripts personnalisés dans le stockage d’objets blob Azure. Il existe deux options pour stocker les ressources de script dans le stockage blob. Rendez le conteneur ou le script public et suivez l’approche illustrée ci-dessus, ou privilégiez le stockage privé d’objets blob qui vous oblige à fournir storageAccountName et storageAccountKey à la définition de ressource CustomScriptExtension.

Dans l’exemple ci-dessous, nous avons franchi une étape supplémentaire. Bien qu’il soit possible de fournir le nom de compte de stockage et la clé en tant que paramètre ou variable pendant le déploiement, les modèles Resource Manager fournissent la fonction `listKeys` qui peut obtenir la clé de compte de stockage par programme et l’insérer dans le modèle pour vous au moment du déploiement.

Dans l’exemple de définition de ressource CustomScriptExtension ci-dessous, notre script personnalisé a déjà été chargé vers un compte de stockage Azure appelé `mystorageaccount9999` qui existe dans un autre groupe de ressources appelé `mysa999rgname`. Lors du déploiement d’un modèle qui contient cette ressource, la fonction `listKeys` obtient par programme la clé de compte de stockage pour le compte de stockage `mystorageaccount9999` dans le groupe de ressources `mysa999rgname` et l’insère pour nous dans le modèle.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://mystorageaccount9999.blob.core.windows.net/container/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]",
      "storageAccountName": "mystorageaccount9999",
      "storageAccountKey": "[listKeys(resourceId('mysa999rgname','Microsoft.Storage/storageAccounts', mystorageaccount9999), '2015-06-15').key1]"
    }
  }
}
```

Le principal avantage de cette approche est qu’elle ne requiert pas la modification de vos paramètres de modèle ou de déploiement en cas de modification de la clé du compte de stockage.

Pour plus d’informations sur l’utilisation des extensions de script personnalisé, consultez [Extensions de script personnalisé avec des modèles Resource Manager](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-step"></a>Étape suivante
<hr>

[Découvrir d’autres modèles Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)


