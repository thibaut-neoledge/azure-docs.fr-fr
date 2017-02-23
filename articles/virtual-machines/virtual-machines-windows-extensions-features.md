---
title: "Extensions et fonctionnalités de machine virtuelle pour Windows | Microsoft Docs"
description: "Découvrez les extensions disponibles pour les machines virtuelles, regroupées par ce qu’ils fournissent ou améliorent."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 1493bf0a545cd605b80404413894bd56e1ced735
ms.openlocfilehash: ebd8734ad2de6f5e528e941f90098de079966f48


---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Extensions et fonctionnalités de machine virtuelle pour Windows

Les extensions de machine virtuelle Azure sont de petites applications permettant d’exécuter des tâches de configuration et d’automatisation post-déploiement sur des machines virtuelles Azure. Par exemple, si une machine virtuelle requiert l’installation d’un logiciel, une protection antivirus ou une configuration de Docker, il est possible d’effectuer ces tâches à l’aide d’une extension de machine virtuelle. Les extensions de machine virtuelle Azure peuvent être exécutées à l’aide de l’interface de ligne de commande Azure, de PowerShell, de modèles Azure Resource Manager et du portail Azure. Les extensions peuvent être associées à un nouveau déploiement de machine virtuelle ou s’exécuter sur tout système existant.

Ce document offre une vue d’ensemble des extensions de machine virtuelle et des composants requis pour utiliser les extensions de machine virtuelle. Il explique également comment détecter, gérer et supprimer les extensions de machine virtuelle. Ce document fournit des informations générales, car de nombreuses extensions de machine virtuelle sont disponibles, chacune présentant une configuration potentiellement unique. Vous trouverez des informations détaillées sur une extension spécifique dans la documentation consacrée à celle-ci.

## <a name="use-cases-and-samples"></a>Cas d’utilisation et exemples

Plusieurs extensions de machine virtuelle Azure sont disponibles, chacune impliquant un cas d’utilisation spécifique. Exemples de cas d’utilisation :

- Appliquer des configurations d’état souhaité PowerShell à une machine virtuelle à l’aide de l’extension DSC pour Windows. Pour plus d’informations sur l’extension DSC Azure, consultez [cette page](virtual-machines-windows-extensions-dsc-overview.md).
- Configurer l’analyse des machines virtuelles avec l’extension de machine virtuelle Microsoft Monitoring Agent. Pour plus d’informations, consultez [Connecter des machines virtuelles Azure à Log Analytics](../log-analytics/log-analytics-azure-vm-extension.md).
- Configurer l’analyse de votre infrastructure Azure à l’aide de l’extension Datadog. Pour plus d’informations, consultez le [blog Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Configurer une machine virtuelle Azure à l’aide de Chef. Pour plus d’informations, consultez [Automatisation du déploiement de machine virtuelle Azure avec Chef](virtual-machines-windows-chef-automation.md).

En plus des extensions propres à des processus, une extension de script personnalisé est disponible pour les machines virtuelles Windows et Linux. L’extension de script personnalisé pour Windows permet d’exécuter n’importe quel script PowerShell sur une machine virtuelle. Cela s’avère utile pour concevoir des déploiements Azure qui nécessitent une configuration plus avancée que celle permise par les outils Azure. Pour plus d’informations sur l’extension de script personnalisé pour les machines virtuelles Windows, consultez [cet article](virtual-machines-windows-extensions-customscript.md).

Pour étudier un exemple d’utilisation d’une extension de machine virtuelle dans le cadre d’un déploiement d’application de bout en bout, consultez [Automatisation des déploiements d’applications sur Machines Virtuelles Azure](virtual-machines-windows-dotnet-core-1-landing.md).

## <a name="prerequisites"></a>Composants requis

Chaque extension de machine virtuelle peut présenter son propre ensemble de composants requis. Par exemple, l’extension de machine virtuelle Docker nécessite une distribution Linux compatible. Les composants requis pour une extension spécifique sont présentés en détail dans la documentation consacrée à celle-ci.

### <a name="azure-vm-agent"></a>Agent de machine virtuelle Azure
L’agent de machine virtuelle Azure gère l’interaction entre une machine virtuelle Azure et le contrôleur de structure Azure. L’agent de machine virtuelle est responsable de nombreux aspects fonctionnels liés au déploiement et à la gestion des machines virtuelles Azure, dont les extensions de machine virtuelle en cours d’exécution. L’agent de machine virtuelle Azure est préinstallé sur les images d’Azure Marketplace et peut être installé sur les systèmes d’exploitation pris en charge.

Pour plus d’informations sur les systèmes d’exploitation pris en charge et sur la procédure d’installation, consultez l’article [Agent de machine virtuelle et extensions Azure](virtual-machines-windows-agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Détecter les extensions de machine virtuelle
De nombreuses extensions de machine virtuelle différentes peuvent être utilisées avec les machines virtuelles Azure. Pour en obtenir la liste complète, exécutez la commande suivante avec le module Azure Resource Manager PowerShell. Veillez à spécifier l’emplacement de votre choix lorsque vous exécutez cette commande.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Exécuter les extensions de machine virtuelle

Les extensions de machine virtuelle peuvent être exécutées sur des machines virtuelles existantes, ce qui s’avère utile lorsque vous devez apporter des modifications de configuration ou restaurer la connectivité sur une machine virtuelle déjà déployée. Les extensions de machines virtuelles peuvent également être intégrées dans des déploiements de modèles Azure Resource Manager. L’utilisation d’extensions avec des modèles Resource Manager permet de déployer et de configurer des machines virtuelles Azure sans avoir à intervenir après le déploiement.

Les méthodes suivantes peuvent être utilisées pour exécuter une extension sur une machine virtuelle existante.

### <a name="powershell"></a>PowerShell

Il existe plusieurs commandes PowerShell pour l’exécution des extensions. Pour afficher la liste, exécutez les commandes PowerShell suivantes.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Vous générez ainsi des informations qui ressemblent à ce qui suit :

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

L’exemple suivant utilise l’extension de script personnalisé pour télécharger un script sur la machine virtuelle cible depuis un référentiel GitHub, puis pour exécuter le script. Pour plus d’informations sur l’extension de script personnalisé, consultez [Vue d’ensemble de l’extension de script personnalisé](virtual-machines-windows-extensions-customscript.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

Dans cet exemple, l’extension d’accès aux machines virtuelles est utilisée pour réinitialiser le mot de passe d’administration d’une machine virtuelle Windows. Pour plus d’informations sur l’extension d’accès aux machines virtuelles, consultez [Réinitialiser le service Bureau à distance pour une machine virtuelle Windows](virtual-machines-windows-reset-rdp.md).

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Vous pouvez utiliser la commande `Set-AzureRmVMExtension` pour démarrer n’importe quelle extension de machine virtuelle. Pour plus d’informations, consultez [Référence Set-AzureRmVMExtension](https://msdn.microsoft.com/en-us/library/mt603745.aspx).


### <a name="azure-portal"></a>Portail Azure

Une extension de machine virtuelle peut être appliquée à une machine virtuelle existante via le portail Azure. Pour ce faire, sélectionnez la machine virtuelle à utiliser, choisissez **Extensions**, puis cliquez sur **Ajouter**. La liste des extensions disponibles s’affiche. Sélectionnez l’extension souhaitée et suivez les étapes de l’Assistant.

L’image suivante illustre l’installation de l’extension Microsoft Antimalware à partir du portail Azure.

![Extension Antimalware](./media/virtual-machines-windows-extensions-features/anti-virus-extension.png)

### <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Les extensions de machine virtuelle peuvent être ajoutées à un modèle Azure Resource Manager et exécutées avec le déploiement du modèle. Le déploiement d’extensions avec un modèle permet de créer des déploiements Azure entièrement configurés. Par exemple, le code JSON suivant est tiré d’un modèle Resource Manager qui déploie un ensemble de machines virtuelles à charge équilibrée et une base de données SQL Azure, puis installe une application .NET Core sur chaque machine virtuelle. L’extension de machine virtuelle se charge de l’installation du logiciel.

Pour plus d’informations, consultez le [modèle Resource Manager complet](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

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

Pour plus d’informations, consultez l’article [Création de modèles Azure Resource Manager avec des extensions de machine virtuelle Windows](virtual-machines-windows-extensions-authoring-templates.md).

## <a name="secure-vm-extension-data"></a>Sécuriser les données des extensions de machine virtuelle

Lorsque vous exécutez une extension de machine virtuelle, vous pouvez avoir besoin d’inclure des informations sensibles telles que des informations d’identification, des noms de compte de stockage et des clés d’accès à des comptes de stockage. De nombreuses extensions de machine virtuelle comprennent une configuration protégée qui chiffre les données et les déchiffre uniquement à l’intérieur de la machine virtuelle cible. Chaque extension possède un schéma spécifique de configuration protégée qui sera présenté en détail dans la documentation consacrée à l’extension.

L’exemple suivant illustre une instance de l’extension de script personnalisé pour Windows. Notez que la commande à exécuter inclut un ensemble d’informations d’identification. Dans cet exemple, la commande à exécuter ne sera pas chiffrée.


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
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Sécurisez la chaîne d’exécution en déplaçant la propriété **command to execute** dans la configuration **protected**.

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

## <a name="troubleshoot-vm-extensions"></a>Résoudre les problèmes liés aux extensions de machine virtuelle

Chaque extension de machine virtuelle peut présenter une procédure de résolution des problèmes spécifique. Par exemple, lorsque vous utilisez l’extension de script personnalisé, les détails de l’exécution du script sont accessibles localement sur la machine virtuelle utilisée pour l’exécution de l’extension. La procédure de résolution des problèmes spécifique d’une extension est présentée en détail dans la documentation de cette dernière.

La procédure de résolution des problèmes ci-dessous s’applique à toutes les extensions de machine virtuelle.

### <a name="view-extension-status"></a>Afficher l’état de l’extension

Après avoir exécuté une extension de machine virtuelle, utilisez la commande PowerShell suivante pour obtenir l’état de l’extension. Remplacez les exemples de noms de paramètre par vos propres valeurs. Le paramètre `Name` accepte le nom donné à l’extension au moment de l’exécution.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

La sortie a l'aspect suivant :

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

L’état d’exécution de l’extension est également visible dans le portail Azure. Pour afficher l’état d’une extension, sélectionnez la machine virtuelle, choisissez **Extensions**, puis sélectionnez l’extension souhaitée.

### <a name="rerun-vm-extensions"></a>Réexécuter les extensions de machine virtuelle

Dans certains cas, il se peut que vous deviez réexécuter une extension de machine virtuelle. Pour ce faire, vous pouvez supprimer l’extension, puis la réexécuter avec la méthode d’exécution de votre choix. Pour supprimer une extension, exécutez la commande suivante avec le module Azure PowerShell. Remplacez les exemples de noms de paramètre par vos propres valeurs.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Une extension peut également être supprimée à l’aide du portail Azure. Pour ce faire :

1. Sélectionnez une machine virtuelle.
2. Sélectionnez **Extensions**.
3. Choisissez l’extension souhaitée.
4. Sélectionnez **Désinstaller**.

## <a name="common-vm-extensions-reference"></a>Informations de référence sur les extensions de machine virtuelle courantes
| Nom de l’extension | Description | Plus d’informations |
| --- | --- | --- |
| Extension de script personnalisé pour Windows |Exécuter des scripts sur une machine virtuelle Azure |[Extension de script personnalisé pour Windows](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Extension DSC pour Windows |Extension PowerShell DSC (Desired State Configuration, configuration d’état souhaité) |[Extension DSC pour Windows](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Extension Diagnostics Azure |Gérer les diagnostics Azure |[Extension Diagnostics Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extension d’accès aux machines virtuelles Azure |Gérer les utilisateurs et les informations d’identification |[Extension d’accès aux machines virtuelles pour Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |



<!--HONumber=Feb17_HO1-->


