---
title: "Créer une machine virtuelle avec un modèle Resource Manager | Microsoft Docs"
description: "Utilisez un modèle Resource Manager et PowerShell pour créer facilement une machine virtuelle Windows."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/06/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: f0112aafb4930e475b2cb8bf399110e480381b95


---
# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources
Cet article vous présente un modèle Azure Resource Manager et vous montre comment utiliser PowerShell pour le déployer. Le modèle déploie une machine virtuelle unique exécutant Windows Server dans un nouveau réseau virtuel avec un seul sous-réseau.

Il vous faudra environ 20 minutes pour effectuer les étapes décrites dans cet article.

> [!IMPORTANT]
> Si vous souhaitez que votre machine virtuelle fasse partie d’un groupe à haute disponibilité, ajoutez-la au groupe lors de sa création. Il n’existe actuellement aucun moyen d’ajouter une machine virtuelle dans un groupe à haute disponibilité, une fois celle-ci créée.
> 
> 

## <a name="step-1-create-the-template-file"></a>Étape 1 : création du fichier de modèle
Vous pouvez créer votre propre modèle à l’aide des informations figurant dans [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md). Vous pouvez également déployer des modèles qui ont été créés pour vous à partir des [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).

1. Ouvrez votre éditeur de texte et ajoutez l’élément de schéma requis ainsi que l’élément contentVersion requis :

  ```json  
  {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  }
  ```

2. Les [paramètres](../resource-group-authoring-templates.md#parameters) ne sont pas toujours nécessaires, mais ils offrent un moyen d’entrer des valeurs lors du déploiement du modèle. Ajoutez l’élément parameters et ses éléments enfants après l’élément contentVersion :

  ```json   
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminUserName": { "type": "string" },
      "adminPassword": { "type": "securestring" }
    },
  }
  ```

3. [variables](../resource-group-authoring-templates.md#variables) peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créées à partir d’une combinaison de valeurs de paramètres. Ajoutez l’élément variables après la section parameters :

  ```json   
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminUsername": { "type": "string" },
      "adminPassword": { "type": "securestring" }
    },
    "variables": {
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
      "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
    },
  }
  ```

4. [ressources](../resource-group-authoring-templates.md#resources) telles que la machine virtuelle, le réseau virtuel et le compte de stockage sont ensuite définis dans le modèle. Ajoutez la section resources après la section variables :

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminUsername": { "type": "string" },
      "adminPassword": { "type": "securestring" }
    },
    "variables": {
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
      "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "mystorage1",
        "apiVersion": "2015-06-15",
        "location": "[resourceGroup().location]",
        "properties": { "accountType": "Standard_LRS" }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "myip1",
        "location": "[resourceGroup().location]",
        "properties": {
          "publicIPAllocationMethod": "Dynamic",
          "dnsSettings": { "domainNameLabel": "mydns1" }
        }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "myvn1",
        "location": "[resourceGroup().location]",
        "properties": {
          "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
          "subnets": [ {
            "name": "mysn1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          } ]
        }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "mync1",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "Microsoft.Network/publicIPAddresses/myip1",
          "Microsoft.Network/virtualNetworks/myvn1"
        ],
        "properties": {
          "ipConfigurations": [ {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
              },
              "subnet": { "id": "[variables('subnetRef')]" }
            }
          } ]
        }
      },
      {
        "apiVersion": "2016-03-30",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "myvm1",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "Microsoft.Network/networkInterfaces/mync1",
          "Microsoft.Storage/storageAccounts/mystorage1"
        ],
        "properties": {
          "hardwareProfile": { "vmSize": "Standard_A1" },
          "osProfile": {
            "computerName": "myvm1",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "storageProfile": {
            "imageReference": {
              "publisher": "MicrosoftWindowsServer",
              "offer": "WindowsServer",
              "sku": "2012-R2-Datacenter",
              "version" : "latest"
            },
            "osDisk": {
              "name": "myosdisk1",
              "vhd": {
                "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
              },
              "caching": "ReadWrite",
              "createOption": "FromImage"
            }
          },
          "networkProfile": {
            "networkInterfaces" : [ {
              "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
            } ]
          }
        }
      } ]
    }
  ```
    
  > [!NOTE]
  > Cet article crée une machine virtuelle exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, consultez [Parcourir et sélectionner des images de machine virtuelle Azure avec Windows PowerShell et l’interface CLI Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

5. Enregistrez le fichier de modèle sous le nom *VirtualMachineTemplate.json*.

## <a name="step-2-create-the-parameters-file"></a>Étape 2 : création du fichier de paramètres
Pour spécifier des valeurs pour les paramètres de ressource qui ont été définis dans le modèle, créez un fichier de paramètres qui contient les valeurs utilisées lors du déploiement du modèle.

1. Dans l’éditeur de texte, copiez ce contenu JSON dans un nouveau fichier appelé *Parameters.json* :

  ```json 
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }
  ```
   
   > [!NOTE]
   > En savoir plus sur les [conditions requises pour les noms d’utilisateur et les mots de passe](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).
   > 
   > 
2. Enregistrez le fichier de paramètres.

## <a name="step-3-install-azure-powershell"></a>Étape 3 : installation d’Azure PowerShell
Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## <a name="step-4-create-a-resource-group"></a>Étape 4 : création d’un groupe de ressources
Toutes les ressources doivent être déployées dans un [groupe de ressources](../azure-resource-manager/resource-group-overview.md).

1. Obtenez la liste des emplacements disponibles où créer des ressources.
   
  ```powershell   
  Get-AzureRmLocation | sort DisplayName | Select DisplayName
  ```
2. Remplacez la valeur de **$locName** par un emplacement de la liste, par exemple **États-Unis du Centre**. Créez la variable.

  ```powershell   
  $locName = "location name"
  ```

3. Remplacez la valeur de **$rgName** par le nom du nouveau groupe de ressources. Créez la variable et le groupe de ressources.

  ```powershell   
  $rgName = "resource group name"
  New-AzureRmResourceGroup -Name $rgName -Location $locName
  ```
   
  Un résultat comme l’exemple suivant devrait s’afficher :

  ```powershell
  ResourceGroupName : myrg1
  Location          : centralus
  ProvisioningState : Succeeded
  Tags              :
  ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
  ```

## <a name="step-5-create-the-resources-with-the-template-and-parameters"></a>Étape 5 : création des ressources avec le modèle et les paramètres
Remplacez la valeur de **$templateFile** par le chemin d’accès et le nom du fichier de modèle. Remplacez la valeur de **$parameterFile** par le chemin d’accès et le nom du fichier de paramètres. Créez les variables, puis déployez le modèle. 

```powershell
$templateFile = "template file"
$parameterFile = "parameter file"
New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

Le résultat suivant doit s’afficher :

```powershell
DeploymentName    : VirtualMachineTemplate
ResourceGroupName : myrg1
ProvisioningState : Succeeded
Timestamp         : 4/14/2016 8:11:37 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     mytestacct1
                    adminPassword    SecureString

Outputs           :
```

> [!NOTE]
> Vous pouvez également déployer des modèles et des paramètres à partir d'un compte de stockage Azure. Pour en savoir plus, consultez la rubrique [Utilisation d’Azure PowerShell avec Azure Storage](../storage/storage-powershell-guide-full.md).
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Si vous rencontrez des problèmes de déploiement, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources avec le portail Azure](../resource-manager-troubleshoot-deployments-portal.md).
* Pour apprendre à gérer la machine virtuelle que vous avez créée, consultez [Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


