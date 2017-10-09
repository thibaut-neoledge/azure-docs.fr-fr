---
title: "Ajouter l’image de machine virtuelle par défaut sur la Place de marché Azure Stack | Microsoft Docs"
description: "Ajoutez l’image de machine virtuelle Windows Server 2016 par défaut sur la Place de marché Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: sngun
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 62f467f1dae5a2cb04e5230ed43b77ec3ec8c1a1
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Ajouter l’image de machine virtuelle Windows Server 2016 sur la Place de marché Azure Stack

Par défaut, il n’y a pas d’images de machine virtuelle disponibles sur la Place de marché Azure Stack. L’opérateur Azure Stack doit ajouter préalablement une image sur la Place de marché pour la mettre à la disposition des utilisateurs. Vous pouvez ajouter l’image Windows Server 2016 sur la Place de marché Azure Stack à l’aide d’une des deux méthodes suivantes :

* [Ajouter l’image en la téléchargeant à partir de la Place de marché Azure](#add-the-image-by-downloading-it-from-the-Azure-marketplace) : choisissez cette méthode si vous êtes dans un scénario connecté et si vous avez inscrit votre instance Azure Stack auprès d’Azure.

* [Ajouter l’image à l’aide de PowerShell](#add-the-image-by-using-powershell) : choisissez cette méthode si vous avez déployé Azure Stack dans un scénario déconnecté ou avec une connectivité limitée.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Ajouter l’image en la téléchargeant à partir de la Place de marché Azure

1. Après avoir déployé Azure Stack, connectez-vous à votre Kit de développement Azure Stack.

2. Cliquez sur **Plus de services** > **Gestion de la Place de Marché** > **Ajouter à partir d’Azure** 

3. Localisez ou recherchez l’image **Windows Server Datacenter 2016 – Évaluations** et cliquez sur **Télécharger**

   ![Télécharger l’image à partir d’Azure](media/azure-stack-add-default-image/download-image.png)

Une fois son téléchargement terminé, l’image est ajoutée au panneau **Gestion de la Place de Marché** et au panneau **Machines virtuelles**.

## <a name="add-the-image-by-using-powershell"></a>Ajouter l’image à l’aide de PowerShell

### <a name="prerequisites"></a>Composants requis 

Effectuez les étapes prérequises suivantes à partir du [Kit de développement](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou à partir d’un client externe Windows si vous êtes [connecté par le biais d’un VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) :

* Installez les [modules Azure PowerShell compatibles avec Azure Stack](azure-stack-powershell-install.md).  

* Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md).  

* Accédez à https://www.microsoft.com/fr-fr/evalcenter/evaluate-windows-server-2016 et téléchargez la version d’évaluation de Windows Server 2016. Quand vous y êtes invité, sélectionnez la version **ISO** du téléchargement. Enregistrez le chemin d’accès à l’emplacement de téléchargement, car vous en aurez besoin plus tard dans cette procédure. Cette étape nécessite une connectivité Internet.  

Effectuez maintenant les étapes suivantes pour ajouter l’image sur la Place de marché Azure Stack :
   
1. Importez les modules Azure Stack Connect et ComputeAdmin à l’aide des commandes suivantes :

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # import the Connect and ComputeAdmin modules   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Connectez-vous à votre environnement Azure Stack. Exécutez le script suivant en fonction du type de déploiement de votre environnement Azure Stack : AAD ou AD FS (veillez à remplacer les valeurs tenantName AAD, le point de terminaison GraphAudience et la valeur ArmEndpoint conformément à la configuration de votre environnement) :  

   a. Pour **AAD**, utilisez l’applet de commande suivante :

   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"
   
   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint

   Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

   $TenantID = Get-AzsDirectoryTenantId `
     -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
     -EnvironmentName AzureStackAdmin

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```

   b. Pour **AD FS**, utilisez l’applet de commande suivante :
    
   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"

   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint

   Set-AzureRmEnvironment `
     -Name "AzureStackAdmin" `
     -GraphAudience $GraphAudience `
     -EnableAdfsAuthentication:$true

   $TenantID = Get-AzsDirectoryTenantId `
     -ADFS 
     -EnvironmentName AzureStackAdmin 

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```
   
3. Ajoutez l’image Windows Server 2016 sur la Place de marché Azure Stack (sans oublier de remplacer la variable *Path_to_ISO* par le chemin d’accès à l’image ISO WS2016 que vous avez téléchargée) :

   ```PowerShell
   $ISOPath = "<Fully_Qualified_Path_to_ISO>"

   # Add a Windows Server 2016 Evaluation VM Image.
   New-AzsServer2016VMImage `
     -ISOPath $ISOPath

   ```

Pour vous assurer que l’image de machine virtuelle Windows Server 2016 contient la dernière mise à jour cumulative, incluez le paramètre `IncludeLatestCU` dans l’applet de commande `New-AzsServer2016VMImage` que vous allez exécuter. Consultez la section [Paramètres](#parameters) pour plus d’informations sur les paramètres autorisés dans l’applet de commande `New-AzsServer2016VMImage`. La publication de l’image sur la Place de marché Azure Stack prend environ une heure. 

## <a name="parameters"></a>Paramètres

|Paramètres New-AzsServer2016VMImage|Requis ?|Description|
|-----|-----|------|
|ISOPath|Oui|Spécifie le chemin d’accès complet à l’image ISO Windows Server 2016 téléchargée.|
|Net35|Non|Ce paramètre vous permet d’installer le Runtime .NET 3.5 sur l’image Windows Server 2016. Par défaut, cette valeur est définie sur true. L’image doit obligatoirement contenir le Runtime .NET 3.5 pour permettre l’installation des fournisseurs de ressources SQL et MYSQL. |
|Version|Non|Ce paramètre vous permet de choisir l’option d’installation des images Windows Server 2016 : **Base**, **Complète** ou **Les deux**. Par défaut, cette valeur est définie sur Complète.|
|VHDSizeInMB|Non|Définit la taille (en Mo) de l’image VHD à ajouter à votre environnement Azure Stack. Par défaut, cette valeur est définie sur 40 960 Mo.|
|CreateGalleryItem|Non|Spécifie si un élément de la Place de marché doit être créé pour l’image Windows Server 2016. Par défaut, cette valeur est définie sur true.|
|location |Non |Spécifie l’emplacement de publication de l’image Windows Server 2016.|
|IncludeLatestCU|Non|Ce paramètre vous permet d’appliquer la dernière mise à jour cumulative de Windows Server 2016 à la nouvelle image VHD.|
|CUUri |Non |Ce paramètre vous permet de choisir la mise à jour cumulative de Windows Server 2016 à partir d’un URI spécifique. |
|CUPath |Non |Ce paramètre vous permet de choisir la mise à jour cumulative de Windows Server 2016 à partir d’un chemin d’accès local. Ce paramètre est utile si vous avez déployé l’instance Azure Stack dans un environnement déconnecté.|

## <a name="next-steps"></a>Étapes suivantes

[Approvisionner une machine virtuelle](azure-stack-provision-vm.md)

