---
title: "Installer et configurer PowerShell pour Azure Stack - Démarrage rapide | Microsoft Docs"
description: "Découvrez comment installer et configurer PowerShell pour Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6e5c420ed80127213e38849ac1999bba199e36c2
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Devenez rapidement opérationnel avec PowerShell dans Azure Stack.

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Cet article est un guide de démarrage rapide pour installer et configurer l’environnement Azure Stack avec PowerShell. La portée du script fourni dans cet article est définie uniquement par l’**opérateur Azure Stack**.

Cet article est une version condensée des étapes décrites dans les articles [Installer PowerShell]( azure-stack-powershell-install.md), [Télécharger les outils]( azure-stack-powershell-download.md) et [Configurer l’environnement PowerShell de l’opérateur Azure Stack]( azure-stack-powershell-configure-admin.md). Les scripts fournis dans cette rubrique permettent de configurer PowerShell pour les environnements Azure Stack qui sont déployés avec Azure Active Directory ou Active Directory Federation Services.  


## <a name="set-up-powershell-for-aad-based-deployments"></a>Configurer PowerShell pour les déploiements basés sur AAD

Connectez-vous à votre Kit de développement Azure Stack, ou à un client externe Windows si vous êtes connecté par le biais d’un VPN. Ouvrez une session PowerShell ISE avec élévation de privilèges et exécutez le script suivant (veillez à mettre à jour les variables TenantName, ArmEndpoint et GraphAudience conformément à la configuration de votre environnement) :

```powershell
# Specify Azure Active Directory tenant name
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.10 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module `
  .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<GraphAuidence endpoint for your environment>"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackAdmin" `
  -ArmEndpoint $ArmEndpoint

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience $GraphAudience

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $TenantName `
  -EnvironmentName AzureStackAdmin

# Sign-in to the operator's portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackAdmin" `
  -TenantId $TenantID 

```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configurer PowerShell pour les déploiements basés sur AD FS 

Connectez-vous à votre Kit de développement Azure Stack, ou à un client externe Windows si vous êtes connecté par le biais d’un VPN. Ouvrez une session PowerShell ISE avec élévation de privilèges et exécutez le script suivant (veillez à mettre à jour les variables ArmEndpoint et GraphAudience conformément à la configuration de votre environnement) :

```powershell

# Set the module repository and the execution policy
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.10 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module `
  .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<GraphAuidence endpoint for your environment>"

# Configure the cloud administrator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackAdmin" `
  -ArmEndpoint $ArmEndpoint

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience $GraphAudience `
  -EnableAdfsAuthentication:$true

$TenantID = Get-AzsDirectoryTenantId `
  -ADFS `
  -EnvironmentName "AzureStackAdmin"

# Sign-in to the operator's portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackAdmin" `
  -TenantId $TenantID 

```

## <a name="test-the-connectivity"></a>Tester la connectivité

Maintenant que vous avez configuré PowerShell, vous pouvez tester la configuration en créant un groupe de ressources :

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

Quand le groupe de ressources est créé, la sortie de l’applet de commande indique que la valeur de la propriété d’état d’approvisionnement est « Succeeded ».

## <a name="next-steps"></a>Étapes suivantes

* [Installer et configurer l’interface de ligne de commande](azure-stack-connect-cli.md)

* [Développer des modèles](user/azure-stack-develop-templates.md)








