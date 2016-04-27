<properties
   pageTitle="Déployer des machines virtuelles avec plusieurs cartes réseau à l’aide d’un modèle dans Resource Manager | Microsoft Azure"
   description="Apprenez à déployer des machines virtuelles à plusieurs cartes réseau à l’aide d’un modèle dans Resource Manager."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

# Déployer des machines virtuelles avec plusieurs cartes réseau à l’aide d’un modèle

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Étant donné que vous ne pouvez pas pour l’instant avoir des machines virtuelles avec une seule carte réseau et des machines virtuelles avec plusieurs cartes réseau dans le même groupe de ressources, vous devrez implémenter les serveurs principaux dans un groupe de ressources et tous les autres composants dans un autre groupe de sécurité. Les étapes ci-dessous utilisent un groupe de ressources nommé *IaaSStory* pour le groupe de ressources principal et *IaaSStory-BackEnd* pour les serveurs principaux.

## Composants requis

Avant de déployer les serveurs principaux, vous devez déployer le groupe de ressources principal avec toutes les ressources nécessaires pour ce scénario. Pour déployer ces ressources, procédez comme suit.

1. Accédez à la [page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Dans la page de modèle, à droite du **groupe de ressources parent**, cliquez sur **Déployer dans Azure**.
3. Si nécessaire, modifiez les valeurs de paramètre, puis suivez les étapes du portail Azure en version préliminaire pour déployer le groupe de ressources.

> [AZURE.IMPORTANT] Assurez-vous que vos noms de compte de stockage sont uniques. Vous ne pouvez pas avoir des noms de compte de stockage en double dans Azure.

## Comprendre le modèle de déploiement

Avant de déployer le modèle fourni avec cette documentation, assurez-vous de bien comprendre sa fonction. Les étapes ci-dessous fournissent une bonne vue d’ensemble du modèle en question.

1. Accédez à la [page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Cliquez sur **azuredeploy.json** pour ouvrir le fichier de modèle.
3. Notez le paramètre *osType* répertorié ci-dessous. Ce paramètre permet de sélectionner l’image de machine virtuelle à utiliser pour le serveur de base de données, ainsi que plusieurs paramètres liés au système d'exploitation.

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

4. Faites défiler la liste des variables et vérifiez la définition des variables **dbVMSetting** répertoriées ci-dessous. Elle reçoit l’un des éléments de tableau contenus dans la variable **dbVMSettings**. Si vous êtes familiarisé avec la terminologie du développement logiciel, vous pouvez afficher la variable **dbVMSettings** en tant que table de hachage ou dictionnaire.

		"dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Supposons que vous décidez de déployer des machines virtuelles Windows exécutant SQL dans le back end. La valeur **osType** est *Windows* et la variable **dbVMSetting** contient l’élément ci-dessous, qui représente la première valeur de la variable **dbVMSettings**.

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

6. Notez que l’élément **vmSize** contient la valeur *Standard\_DS3*. Seules certaines tailles de machine virtuelle permettent l’utilisation de plusieurs cartes réseau. Vous pouvez vérifier quelles tailles de machine virtuelle ont plusieurs cartes réseau activées en consultant la [vue d’ensemble relative aux multiples cartes réseau](virtual-networks-multiple-nics.md).
7. Faites défiler jusqu’à **resources** et notez le premier élément. Il décrit un compte de stockage. Ce compte de stockage permet de gérer les disques de données utilisés par chaque machine virtuelle de la base de données. Dans ce scénario, chaque machine virtuelle de base de données possède un disque de système d’exploitation stocké dans le stockage standard et deux disques de données stockés dans le stockage SSD (Premium).

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

8. Faites défiler jusqu’à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente la carte réseau utilisée pour l’accès à la base de données dans chaque machine virtuelle de base de données. Notez l’utilisation de la fonction **copy** dans cette ressource. Le modèle vous permet de déployer autant de machines virtuelles que vous le souhaitez, en fonction du paramètre **dbCount**. Par conséquent, vous devez créer le même nombre de cartes réseau pour l’accès à la base de données, une pour chaque machine virtuelle.

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

9. Faites défiler jusqu’à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente la carte réseau utilisée pour la gestion dans chaque machine virtuelle de base de données. Une fois encore, vous devez avoir une carte réseau pour chaque machine virtuelle de base de données. Notez l’élément **networkSecurityGroup** qui lie un groupe de sécurité réseau qui permet d’accéder à RDP/SSH sur cette carte réseau uniquement.

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

10. Faites défiler jusqu’à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente un groupe à haute disponibilité devant être partagé par toutes les machines virtuelles de base de données. De cette façon, vous garantissez qu’il y aura toujours une machine virtuelle du groupe qui sera en cours d’exécution lors de la maintenance.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Compute/availabilitySets",
	      "name": "[variables('dbVMSetting').avsetName]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "AvailabilitySet - DB"
	      }
	    },

11. Faites défiler jusqu’à la ressource suivante. Cette ressource représente les machines virtuelles de base de données, comme illustré dans les premières lignes répertoriées ci-dessous. Notez à nouveau l’utilisation de la fonction **copy** qui permet de s’assurer que plusieurs machines virtuelles sont créées en fonction du paramètre **dbCount**. Notez également la collection **dependsOn**. Elle répertorie les deux cartes réseau qui doivent être créées avant le déploiement de la machine virtuelle, ainsi que le groupe à haute disponibilité et le compte de stockage.

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

12. Faites défiler la ressource de machine virtuelle jusqu’à l’élément **networkProfile**, comme indiqué ci-dessous. Notez que deux cartes réseau sont référencées pour chaque machine virtuelle. Lorsque vous créez plusieurs cartes réseau pour une machine virtuelle, vous devez définir la propriété **primary** de l’une des cartes réseau sur *true* et celle des autres cartes réseau sur *false*.

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
      }

## Déployer le modèle ARM en cliquant pour déployer

> [AZURE.IMPORTANT] Assurez-vous de suivre les [conditions préalables](#Pre-requisites) avant de suivre les instructions ci-dessous.

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle en un clic, suivez [ce lien](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), à droite du **groupe de ressources du back end (voir la documentation)**, cliquez sur **Déployer dans Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, puis suivez les instructions dans le portail.

La figure ci-dessous illustre le contenu du nouveau groupe de ressources après le déploiement.

![Groupe de ressources du back end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## Déployer le modèle à l’aide de PowerShell

Pour déployer le modèle téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Pour créer un groupe de ressources à l'aide du modèle, exécutez l'applet de commande **`New-AzureRmResourceGroup`**.

		New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'

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

## Déployer le modèle à l’aide de l’interface de ligne de commande Azure

Pour déployer le modèle à l’aide de l’interface de ligne de commande Azure, procédez comme suit.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](../xplat-cli-install.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Exécutez la commande **`azure config mode`** pour passer en mode Resource Manager, comme illustré ci-dessous.

		azure config mode arm

	Voici le résultat attendu pour la commande ci-dessus :

		info:    New mode is arm

3. Ouvrez le [fichier de paramètres](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), sélectionnez son contenu et enregistrez-le dans un fichier sur votre ordinateur. Pour cet exemple, nous avons enregistré le fichier de paramètres sous le nom *parameters.json*.

4. Exécutez l'applet de commande **`azure group deployment create`** pour déployer le nouveau réseau virtuel à l'aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus haut. La liste affichée après le résultat présente les différents paramètres utilisés.

		azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

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

<!---HONumber=AcomDC_0413_2016-->