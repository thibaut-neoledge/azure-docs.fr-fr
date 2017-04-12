---
title: "Créer une machine virtuelle avec plusieurs cartes réseau - Modèle Azure Resource Manager | Microsoft Docs"
description: "Créez une machine virtuelle avec plusieurs cartes réseau à l’aide d’un modèle Azure Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 99292dedfd83c0adde6b7352e08e7ae3b0b14513
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>Créer une machine virtuelle avec plusieurs cartes réseau à l’aide d’un modèle
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du [modèle de déploiement classique](virtual-network-deploy-multinic-classic-ps.md).
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Les étapes suivantes utilisent un groupe de ressources nommé *IaaSStory* pour les serveurs web et un groupe de ressources nommé *IaaSStory-BackEnd* pour les serveurs de base de données.

## <a name="prerequisites"></a>Composants requis
Avant de créer les serveurs de base de données, vous devez créer le groupe de ressources *IaaSStory* avec toutes les ressources nécessaires pour ce scénario. Pour créer ces ressources, exécutez les étapes suivantes :

1. Accédez à la [page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Dans la page de modèle, à droite du **groupe de ressources parent**, cliquez sur **Déployer sur Azure**.
3. Si nécessaire, modifiez les valeurs de paramètre, puis suivez les étapes du portail Azure en version préliminaire pour déployer le groupe de ressources.

> [!IMPORTANT]
> Assurez-vous que vos noms de compte de stockage sont uniques. Vous ne pouvez pas avoir des noms de compte de stockage en double dans Azure.
> 

## <a name="understand-the-deployment-template"></a>Comprendre le modèle de déploiement
Avant de déployer le modèle fourni avec cette documentation, assurez-vous de bien comprendre sa fonction. Les étapes ci-dessous donnent un bon aperçu du modèle :

1. Accédez à la [page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Cliquez sur **azuredeploy.json** pour ouvrir le fichier de modèle.
3. Notez le paramètre *osType* répertorié ci-dessous. Ce paramètre permet de sélectionner l’image de machine virtuelle à utiliser pour le serveur de base de données, ainsi que plusieurs paramètres liés au système d'exploitation.

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. Faites défiler la liste des variables et vérifiez la définition des variables **dbVMSetting** répertoriées ci-dessous. Elle reçoit l’un des éléments de tableau contenus dans la variable **dbVMSettings** . Si la terminologie du développement de logiciels vous est familière, vous pouvez envisager la variable **dbVMSettings** comme une table de hachage ou un dictionnaire.

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. Supposons que vous décidez de déployer des machines virtuelles Windows exécutant SQL sur le serveur principal. La valeur du paramètre **osType** est *Windows* et la variable **dbVMSetting** contient l’élément ci-dessous, qui représente la première valeur de la variable **dbVMSettings**.

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. Notez que l’élément **vmSize** contient la valeur *Standard_DS3*. Seules certaines tailles de machine virtuelle permettent l’utilisation de plusieurs cartes réseau. Vous pouvez vérifier les tailles de machine virtuelle qui prennent en charge plusieurs cartes réseau en lisant les articles [Tailles des machines virtuelles Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Tailles des machines virtuelles Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

7. Faites défiler jusqu’à **resources** et notez le premier élément. Il décrit un compte de stockage. Ce compte de stockage permet de gérer les disques de données utilisés par chaque machine virtuelle de la base de données. Dans ce scénario, chaque machine virtuelle de base de données possède un disque de système d’exploitation stocké dans le stockage standard et deux disques de données stockés dans le stockage SSD (Premium).

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. Faites défiler jusqu’à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente la carte réseau utilisée pour l’accès à la base de données dans chaque machine virtuelle de base de données. Notez l’utilisation de la fonction **copy** dans cette ressource. Le modèle vous permet de déployer autant de machines virtuelles que vous le souhaitez, en fonction du paramètre **dbCount** . Par conséquent, vous devez créer le même nombre de cartes réseau pour l’accès à la base de données, une pour chaque machine virtuelle.

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. Faites défiler jusqu’à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente la carte réseau utilisée pour la gestion dans chaque machine virtuelle de base de données. Une fois encore, vous devez avoir une carte réseau pour chaque machine virtuelle de base de données. Notez l’élément **networkSecurityGroup** qui lie un groupe de sécurité réseau qui permet d’accéder à RDP/SSH sur cette carte réseau uniquement.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. Faites défiler jusqu’à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente un groupe à haute disponibilité devant être partagé par toutes les machines virtuelles de base de données. De cette façon, vous garantissez qu’il y aura toujours une machine virtuelle du groupe qui sera en cours d’exécution lors de la maintenance.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. Faites défiler jusqu’à la ressource suivante. Cette ressource représente les machines virtuelles de base de données, comme illustré dans les premières lignes répertoriées ci-dessous. Comme vous pouvez le constater, la fonction **copy** est à nouveau utilisée. Les diverses machines virtuelles sont donc créées en tenant compte du paramètre **dbCount**. Notez également la collection **dependsOn** . Elle répertorie les deux cartes réseau qui doivent être créées avant le déploiement de la machine virtuelle, ainsi que le groupe à haute disponibilité et le compte de stockage.

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. Faites défiler la ressource de machine virtuelle jusqu’à l’élément **networkProfile** , comme indiqué ci-dessous. Notez que deux cartes réseau sont référencées pour chaque machine virtuelle. Quand vous créez plusieurs cartes réseau pour une machine virtuelle, vous devez attribuer la valeur *true* à la propriété **primary** de l’une des cartes réseau et la valeur *false* aux autres cartes.

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Déployer le modèle ARM en cliquant pour déployer

> [!IMPORTANT]
> Assurez-vous de suivre les [conditions préalables](#Pre-requisites) avant de suivre les instructions ci-dessous.
> 

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle en un clic, suivez [ce lien](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), à droite du **groupe de ressources du back-end (voir la documentation)**, cliquez sur **Déployer sur Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, puis suivez les instructions dans le portail.

La figure ci-dessous illustre le contenu du nouveau groupe de ressources après le déploiement.

![Groupe de ressources du back end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Déployer le modèle à l’aide de PowerShell
Pour déployer le modèle que vous avez téléchargé à l’aide de PowerShell, installez et configurez PowerShell en effectuant les étapes décrites dans l’article [Installation et configuration de PowerShell](/powershell/azureps-cmdlets-docs), puis procédez comme suit :

Pour créer un groupe de ressources à l'aide du modèle, exécutez l'applet de commande **`New-AzureRmResourceGroup`** .

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

Sortie attendue :

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Déployer le modèle à l’aide de l’interface de ligne de commande Azure
Pour déployer le modèle à l’aide de l’interface de ligne de commande Azure, procédez comme suit.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Exécutez la commande **`azure config mode`** pour passer en mode Resource Manager, comme illustré ci-dessous.

    ```azurecli
    azure config mode arm
    ```

    Sortie attendue :

        info:    New mode is arm

3. Ouvrez le [fichier de paramètres](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), sélectionnez son contenu et enregistrez-le dans un fichier sur votre ordinateur. Pour cet exemple, nous avons enregistré le fichier de paramètres sous le nom *parameters.json*.
4. Exécutez l'applet de commande **`azure group deployment create`** pour déployer le nouveau réseau virtuel à l'aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus haut. La liste affichée après le résultat présente les différents paramètres utilisés.

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    Sortie attendue :
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK


